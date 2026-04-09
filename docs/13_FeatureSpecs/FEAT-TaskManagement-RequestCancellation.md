---
type: feature_spec
id: FEAT-TaskManagement-RequestCancellation
version: "1.0.0"
module: Task Management
milestone: null
status: implemented
description: "Allow Company Super Admins to cancel pending user requests before they are reviewed"
gitlab_issue: "http://gitlab.local:8080/root/artifacts/-/issues/6"
covered_by_apidoc: "[[APIDOC-v1.0.0]]"
source_frs: ["[[FRS-5]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-System]]"]
linked_entities: ["[[ENT-Request]]"]
linked_commands: ["[[CMD-CancelRequest]]"]
linked_flows: ["[[FLOW-RequestCancellation]]"]
linked_states: ["[[STATE-RequestLifecycle]]"]
linked_decisions: ["[[DEC-CancellationPolicy]]"]
linked_integrations: []
rejected_reason: ""
superseded_by: ""
---

# FEAT-TaskManagement-RequestCancellation — Request Cancellation

## Summary

This feature empowers Company Super Admins to withdraw pending user account requests before they enter the review queue. By providing a simple cancellation mechanism with clear state validation and confirmation, the system improves operational flexibility while maintaining data integrity through ownership validation and audit logging. The core value proposition: admins retain control over their requests until final approval, reducing administrative overhead and improving user experience.

## Tasks

---

### Task 1 — Request Cancellation Workflow `[FRS-5]`

**Source:** [[FRS-5]]  
**Depends on:** —  
**Nodes:** [[ENT-Request]], [[STATE-RequestLifecycle]], [[CMD-CancelRequest]], [[FLOW-RequestCancellation]], [[ACT-CompanySuperAdmin]], [[ACT-System]], [[DEC-CancellationPolicy]]

This task implements the complete request cancellation flow: validating request ownership and status, updating the request entity to a terminal cancelled state, recording the cancellation timestamp, and removing the request from actionable views. The technical unit of work is the FLOW-RequestCancellation orchestration with its associated CMD-CancelRequest command, the REQUEST entity lifecycle state machine (STATE-RequestLifecycle), and the cancellation business policy (DEC-CancellationPolicy).

**Technical Scope**

- [[ENT-Request]] entity with fields: id, owner_id, status (enum: pending/approved/rejected/cancelled), created_at, cancelled_at
- [[STATE-RequestLifecycle]] FSM: `pending` → `cancelled` (terminal); other transitions (approved, rejected) handled separately
- [[CMD-CancelRequest]] command: atomic operation validating ownership (`owner_id == actor_id`), status (`status == pending`), updating status to `cancelled`, and recording `cancelled_at` timestamp
- [[FLOW-RequestCancellation]] orchestration: UI trigger → validation → state mutation → confirmation
- [[DEC-CancellationPolicy]] business rule: cancellation is terminal and irreversible; only pending requests eligible

**Acceptance Criteria**

- [ ] Given a Company Super Admin viewing their pending request, when they click "Cancel" and confirm, then request status transitions from `pending` to `cancelled` within 1 second
- [ ] Cancelled requests no longer appear in pending request filter views; only appear in completed/cancelled archive
- [ ] System records `cancelled_at` timestamp on request entity for audit purposes
- [ ] If user attempts to cancel a request they don't own, system returns error `UNAUTHORIZED` with message "You cannot cancel this request" and request remains unchanged
- [ ] If user attempts to cancel a request with status != `pending`, system returns error `INVALID_STATE` with message "Only pending requests can be cancelled" and request remains unchanged
- [ ] Cancellation action is logged to audit trail with request ID, admin ID, and timestamp
- [ ] Confirmation message displays: "Request cancelled successfully"
- [ ] UI state update is idempotent: repeated cancellation attempts result in same final state

**Shadow QA**

→ [[FLOW-RequestCancellation#Shadow-QA]]

---

## Performance Contracts

| Operation | Target | Measurement Point | Source |
|-----------|--------|-------------------|--------|
| Cancel pending request | ≤ 1000ms p99 | API response time | No external integration; local DB write |

## Out of Scope

- **Undoing cancellation:** Once cancelled, requests cannot be reverted; cancellation is terminal
- **Cancelling approved/rejected requests:** Only pending requests are eligible for cancellation
- **Batch cancellation:** Single-request cancellation only; bulk operations handled separately if needed
- **Email notification on cancellation:** Audit logging only; notifications not in scope for v1

## Open Questions

- **[BA, 2026-04-09]** Should we notify the bank reviewer that a pending request was cancelled, or only log it?
- **[BA, 2026-04-09]** What is the data retention policy for cancelled requests? Archive forever or purge after N days?

## Risks

- **Accidental cancellation:** Users might cancel requests unintentionally; the confirmation modal helps but UX testing recommended
- **Data consistency:** Concurrent modifications (request getting approved while cancellation is in flight) need handling; recommend STRICT flow with rollback

## Notes

- Idempotency key not needed since cancellation is final and single-attempt; system should return success if request already cancelled
- Cancellation is marked in the REQUEST audit trail with full provenance (who, when, what)
- No compensating transaction needed; cancellation does not trigger downstream processes (no email, no user creation)
