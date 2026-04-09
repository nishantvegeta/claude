---
type: flow
id: FLOW-RequestCancellation
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "Process by which a Company Super Admin cancels a pending user request"
source_frs: "[[FRS-5]]"
linked_commands: ["[[CMD-CancelRequest]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-System]]"]
linked_entities: ["[[ENT-Request]]"]
linked_states: ["[[STATE-RequestLifecycle]]"]
---

# FLOW-RequestCancellation

Request Cancellation is a business process triggered when a Company Super Admin wants to withdraw a pending user account request before it is reviewed by the bank. The flow is straightforward: the admin confirms their intent, the system validates ownership and status, and the request is marked as cancelled with a timestamp.

## Sequence

```
1. Admin views request tracking list
   ↓
2. Admin clicks "Cancel" button on a pending request
   ↓
3. System displays confirmation modal: "Are you sure you want to cancel this request?"
   ↓
4. Admin confirms cancellation
   ↓
5. System invokes CMD-CancelRequest
   ├─→ Validate: request belongs to admin's organization
   ├─→ Validate: request status = pending
   ├─→ Update: status → cancelled
   ├─→ Update: cancelled_at → current timestamp
   └─→ Log: audit entry with user ID and timestamp
   ↓
6. System returns success message
   ↓
7. UI updates request status badge to "Cancelled" (gray)
   ↓
8. Request no longer appears in "pending" filter view
```

## Actors Involved

- **[[ACT-CompanySuperAdmin]]** — Issues cancellation request; must be request owner
- **[[ACT-System]]** — Validates ownership and status, persists state change

## Entities Involved

- **[[ENT-Request]]** — Target entity transitioning from `pending` to `cancelled` state

## Commands Invoked

- **[[CMD-CancelRequest]]** — Atomic state mutation

## Preconditions

- Company Super Admin is authenticated with valid portal session
- Request list is displayed and loaded
- At least one request exists with status = `pending`
- Admin has identified a request they want to cancel

## Success Conditions

- Request status changes from `pending` to `cancelled`
- Cancellation timestamp is recorded
- User sees confirmation message
- Request disappears from "pending" view on next refresh
- Audit log contains cancellation event

## Failure Paths

| Scenario | Result | Recovery |
|----------|--------|----------|
| Request already processed | "Only pending requests can be cancelled" | Show current status; suggest cancelling different request |
| Ownership validation fails | "You cannot cancel this request" | Verify request belongs to user's organization |
| Network error during save | "Save failed. Please try again." | Retry after network recovery |
| Concurrent cancellation | "Request already cancelled" | Show updated status; treat as success (idempotent) |

## State Transitions

Request transitions within [[STATE-RequestLifecycle]]:
- `pending` → `cancelled` (this flow)
- Other transitions (approved, rejected) handled by separate flows

## Shadow QA

> **Single source of truth.** Feature Specs reference this section via `[[FLOW-RequestCancellation#Shadow-QA]]`. Do not duplicate.

**Happy Path:** Given [[ACT-CompanySuperAdmin]] with authenticated session viewing request with status=`pending` belonging to their organization, when [[CMD-CancelRequest]] fires with valid request ID, then [[ENT-Request]] status transitions to `cancelled`, `cancelled_at` timestamp is recorded, request disappears from pending view on next refresh, and user sees "Request cancelled successfully" confirmation message.

**Edge Case — Non-Owner Cancellation Attempt:** Given [[ACT-CompanySuperAdmin]] attempting to cancel a request not belonging to their organization, when [[CMD-CancelRequest]] fires, then command returns error `UNAUTHORIZED`, [[ENT-Request]] status remains `pending` (no state change), and user sees error "You cannot cancel this request".

**Fault Path — Already Processed Request:** Given request with status=`approved` or `rejected`, when [[CMD-CancelRequest]] fires with that request ID, then command returns error `INVALID_STATE`, request remains in its current state, and user sees "Only pending requests can be cancelled".

