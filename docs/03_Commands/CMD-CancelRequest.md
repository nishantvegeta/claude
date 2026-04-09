---
type: command
id: CMD-CancelRequest
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "Cancels a pending user request and updates its status to cancelled"
source_frs: "[[FRS-5]]"
linked_flows: ["[[FLOW-RequestCancellation]]"]
linked_entities: ["[[ENT-Request]]"]
---

# CMD-CancelRequest

Cancels a pending user request, transitioning it from `pending` status to `cancelled`. This command is issued by a Company Super Admin through the customer portal when they no longer want to proceed with a user account request. It atomically updates the request entity and records a cancellation timestamp.

## Contract

**Input**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| request_id | uuid | yes | Must exist and belong to authenticated user's organization |
| company_id | uuid | yes | Must match authenticated user's organization |

**Output**

| Field | Type | Notes |
|-------|------|-------|
| request_id | uuid | ID of cancelled request |
| status | enum | Always `cancelled` on success |
| cancelled_at | timestamp (UTC) | When cancellation was recorded |
| confirmation_message | string | "Request successfully cancelled" |

## Conditions

**Preconditions:**
- User is authenticated as Company Super Admin
- Request exists with status = `pending`
- Request belongs to user's organization (authorization check)
- No bank reviewer has begun processing the request

**Postconditions:**
- Request status changed from `pending` to `cancelled`
- `cancelled_at` timestamp is set to current UTC time
- Request no longer appears in "actionable" lists for bank reviewers
- Cancellation logged for audit trail with user ID and timestamp
- Confirmation message returned to user

## Error Cases

| Error Code | Condition | Recovery |
|------------|-----------|----------|
| `REQUEST_NOT_FOUND` | Request ID does not exist | Verify request ID and refresh list |
| `UNAUTHORIZED` | Request belongs to different organization | Verify user organization |
| `INVALID_STATE` | Request status is not `pending` | Only pending requests can be cancelled |
| `ALREADY_CANCELLED` | Request was already cancelled | Show status in UI (idempotent safe) |

## Idempotency

This command is idempotent. Sending the same cancellation request twice with the same `request_id` is safe and returns the same result without duplicate state changes. The second invocation detects the request is already cancelled and returns success.

## Notes

- SLA: 99% availability, <200ms response time
- No compensation needed; cancellation is a simple state transition with no side effects
- All cancellations are immutable; cannot be reversed (no "un-cancel" operation)
- Audit log entry includes: user ID, company ID, request ID, cancellation timestamp, IP address

