---
type: entity
id: ENT-Request
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "User request entity that tracks submission, approval, and cancellation lifecycle"
source_frs: "[[FRS-5]]"
linked_states: ["[[STATE-RequestLifecycle]]"]
linked_ui_specs: ["[[VM-RequestTrackingList]]", "[[VM-RequestForm]]"]
---

# ENT-Request

Request is the core domain entity representing a user account creation or modification request submitted by a company administrator. It has a complete lifecycle managed by STATE-RequestLifecycle, progressing from submission through bank review to approval, rejection, or cancellation. Requests are immutable once submitted but their status transitions follow defined rules.

## Attributes

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | uuid | yes | System-generated request ID |
| company_id | FK → ENT-Company | yes | Organization that submitted request |
| request_id | string | yes | Human-readable identifier (e.g., REQ-2026-001) |
| status | enum | yes | `pending`, `approved`, `rejected`, `cancelled` — see [[STATE-RequestLifecycle]] |
| requester_name | string | yes | Name of person being requested for account |
| requester_email | string | yes | Email of new user account |
| requester_role | enum | yes | Target role: `company_super_admin`, `company_user` |
| remark | string | no | Optional notes from requester (max 500 chars) |
| submitted_at | timestamp (UTC) | yes | When request was created |
| cancelled_at | timestamp (UTC) | no | When request was cancelled (null if not cancelled) |
| reviewed_at | timestamp (UTC) | no | When bank reviewer processed request |
| reviewer_id | FK → User | no | Bank reviewer who approved/rejected |
| rejection_reason | string | no | Why request was rejected (only if status = rejected) |

## Invariants

- **Status immutability:** Once status changes from `pending`, only `cancelled` to `pending` reversal is NOT allowed—terminal states are final.
- **Cancellation window:** Can only cancel when status = `pending`. Cannot cancel approved, rejected, or previously cancelled requests.
- **Timestamp ordering:** `submitted_at` ≤ `reviewed_at` ≤ `cancelled_at` (when applicable).
- **Rejection reason:** Must be populated when status = `rejected`; must be null otherwise.
- **Reviewer constraint:** `reviewer_id` and `reviewed_at` are both set or both null (no partial review records).

