---
type: decision
id: DEC-CancellationPolicy
version: "1.0.0"
module: Task Management
milestone: null
status: approved
description: "Cancellation is only allowed for pending requests; no reversal is permitted"
source_frs: "[[FRS-5]]"
linked_entities: ["[[ENT-Request]]"]
linked_flows: ["[[FLOW-RequestCancellation]]"]
---

# DEC-CancellationPolicy

Cancellation Policy codifies the business rule that pending user requests may be cancelled by their originating organization before bank review, but once a request reaches `approved`, `rejected`, or `cancelled` status, it is terminal and cannot be reversed or re-opened.

## Decision

**Requests can only be cancelled while in `pending` status.**

Once a request has been processed by the bank (approved or rejected) or has already been cancelled, the operation is final. No "undo" or "re-submit" mechanism exists.

## Rationale

1. **Simplicity:** Terminal states reduce operational complexity and prevent state explosion.
2. **Audit clarity:** Each request has a clear outcome. Reversals would blur accountability.
3. **Separation of concerns:** If an admin needs to submit a modified request, they must create a new one rather than revive an old one.
4. **Compliance:** Immutable status trail supports regulatory and audit requirements.

## Implications

- **For admins:** If a pending request is cancelled by mistake, the admin must submit a new request.
- **For bank reviewers:** They never encounter cancelled requests (filtering removes them from workflow).
- **For system:** No compensating transactions needed; cancellation is a simple state write.

## Alternatives Considered

| Alternative | Reason Rejected |
|-------------|-----------------|
| Allow "Undo" within X minutes | Introduces complexity; time window is arbitrary; audit trail confusion |
| Allow "Re-submit cancelled" | Violates immutability; requires versioning; ambiguous user experience |
| Create new "draft" state for editable requests | Out of scope; FRS-5 specifies submission → cancellation only |

## Related Constraints

- Cancellation timestamp is immutable (no modification after initial set)
- Cancellation can only be initiated by the requesting organization (not by other admins or bank)
- [[STATE-RequestLifecycle]] enforces terminal state invariant

