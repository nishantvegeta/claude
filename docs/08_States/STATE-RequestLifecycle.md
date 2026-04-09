---
type: state
id: STATE-RequestLifecycle
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "Finite state machine governing the lifecycle of user requests from submission through approval, rejection, or cancellation"
source_frs: "[[FRS-5]]"
linked_entities: ["[[ENT-Request]]"]
linked_flows: ["[[FLOW-RequestCancellation]]"]
---

# STATE-RequestLifecycle

Request Lifecycle defines the complete state machine for ENT-Request. Requests progress from `pending` (newly submitted) through `approved` (bank review passed) or `rejected` (bank review failed), or `cancelled` (admin withdrew before review). Once a request reaches an approval, rejection, or cancellation state, it becomes immutable.

## States

| State | Description | Transitions | Terminal? |
|-------|-------------|-------------|-----------|
| `pending` | Request submitted, awaiting bank review | → approved, rejected, cancelled | No |
| `approved` | Bank reviewer approved the request; user account will be created | → (none) | Yes |
| `rejected` | Bank reviewer rejected the request with explanation | → (none) | Yes |
| `cancelled` | Company admin cancelled before review | → (none) | Yes |

## State Machine Diagram

```
┌─────────────────────────────────────────┐
│           START: pending                │
│   (Request submitted, awaiting review)  │
└──────────────┬──────────────────────────┘
               │
        ┌──────┴──────┬──────────┐
        │             │          │
        ▼             ▼          ▼
    approved      rejected   cancelled
    (terminal)    (terminal) (terminal)
```

## Transitions

### pending → approved
- **Triggered by:** Bank reviewer clicks "Approve" button in approval workflow
- **Preconditions:** Request status = `pending`
- **Side effects:** System generates User Login ID, creates User entity, sends invitation email
- **Postconditions:** Request status = `approved`, User created with status `invited`

### pending → rejected
- **Triggered by:** Bank reviewer clicks "Reject" button in approval workflow
- **Preconditions:** Request status = `pending`, rejection_reason provided
- **Side effects:** No User entity created; notification may be sent to requester
- **Postconditions:** Request status = `rejected`, rejection_reason populated

### pending → cancelled
- **Triggered by:** [[CMD-CancelRequest]] issued by Company Super Admin
- **Preconditions:** Request status = `pending`, request belongs to authenticated user's organization
- **Side effects:** None (no cascade, no notifications sent)
- **Postconditions:** Request status = `cancelled`, cancelled_at timestamp recorded

## Invariants

- **Immutability after terminal:** Once status is `approved`, `rejected`, or `cancelled`, no further transitions allowed.
- **Timestamp ordering:** `submitted_at` ≤ `reviewed_at` and `submitted_at` ≤ `cancelled_at` (when applicable).
- **Rejection reason:** Populated if and only if status = `rejected`.
- **Single path:** A request follows one path through the state machine; cannot backtrack or branch.

## Related Flows

- [[FLOW-RequestCancellation]] — Handles pending → cancelled transition
- [[FLOW-RequestApproval]] — Handles pending → approved transition (external flow)
- [[FLOW-RequestRejection]] — Handles pending → rejected transition (external flow)

