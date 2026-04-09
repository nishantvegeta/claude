---
type: state
id: STATE-RequestStatus
version: "1.0.0"
module: Request Detail View
milestone: null
status: draft
description: "Request approval status state machine for viewing request details"
source_frs: "[[FRS-UC-00008]]"
linked_entities: ["[[ENT-Request]]"]
---

# STATE-RequestStatus

STATE-RequestStatus defines the enumerated states that [[ENT-Request]] can occupy in the request detail view context. This state machine captures the three terminal states of a request: pending review, approved, and rejected. Unlike [[STATE-RequestLifecycle]] (which governs the complete lifecycle including cancellation), STATE-RequestStatus is specifically concerned with the approval outcome states visible to both company administrators and bank reviewers.

## States

| State | Meaning | Precondition | Postcondition | Terminal? |
|-------|---------|--------------|---------------|-----------|
| `pending` | Request submitted and awaiting bank review | ENT-Request is created and submitted | Bank reviewer has not yet processed the request | No |
| `approved` | Request has been approved by bank reviewer | Request is in `pending` state | User account creation workflow proceeds; request is immutable | Yes |
| `rejected` | Request has been rejected by bank reviewer with reason | Request is in `pending` state | Rejection reason is populated; request is immutable; user may submit new request | Yes |

## Visibility Rules

- **Company Super Admin:** Can view requests in any state. Sees their own organization's requests only.
- **Bank Reviewer:** Can view requests in any state. Sees all requests in the system (no org restriction).
- **System:** Displays status badge with color coding:
  - `pending` → yellow/amber badge
  - `approved` → green badge
  - `rejected` → red badge

## Immutability

Once a request transitions to `approved` or `rejected`, the state is terminal and cannot be changed. Requests cannot revert from approved/rejected back to pending. This ensures audit trail integrity and prevents unauthorized state manipulation.

## Related State Machines

- [[STATE-RequestLifecycle]]: Governs the complete request lifecycle including cancellation. STATE-RequestStatus is a simplified view focused on approval outcomes.
