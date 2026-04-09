---
status: absorbed
ingest_timestamp: 2026-04-09T10:25:00Z
absorb_timestamp: 2026-04-09T10:35:00Z
source_type: frs
intended_nodes:
  - ACT-CompanySuperAdmin
  - ACT-System
  - ENT-Request
  - CMD-CancelRequest
  - FLOW-RequestCancellation
  - STATE-RequestLifecycle
  - DEC-CancellationPolicy
absorbed_node_ids:
  - ACT-CompanySuperAdmin
  - ACT-System
  - ENT-Request
  - CMD-CancelRequest
  - FLOW-RequestCancellation
  - STATE-RequestLifecycle
  - DEC-CancellationPolicy
pre_scan_conflict: false
candidate_count: 7
absorbed_count: 7
cnf_created: 0
---

# Staging Entry: FRS-5

**Source:** Issue #5 from root/artifacts  
**Fetched:** 2026-04-09  
**Status:** STAGED (ready for ABSORB)  
**Monolith Check:** ✓ PASSED (0 signals detected)

## Candidate Nodes

| Type | Name | Purpose |
|------|------|---------|
| ACT- | CompanySuperAdmin | Initiates cancellation |
| ACT- | System | Processes status update |
| ENT- | Request | Primary entity with lifecycle |
| CMD- | CancelRequest | Cancellation command |
| FLOW- | RequestCancellation | Business process workflow |
| STATE- | RequestLifecycle | State machine (pending → cancelled) |
| DEC- | CancellationPolicy | Authorization rules |

## Key Requirements

**Primary Flow:**
1. User views request list
2. Clicks "Cancel" on pending request
3. Confirms via modal
4. System updates status → "cancelled"
5. List refreshes

**Validation:**
- Only request owner can cancel
- Only pending requests can be cancelled
- Timestamp recorded for audit

**State Transition:**
- Request: `pending` → `cancelled` (only valid transition for cancellation)

## Pre-Scan Conflicts

✓ No cross-existing node contradictions detected

## Next Steps

1. Run `absorb 5` to compile 7 candidates → DDD nodes
2. Module assignment: Task Management (from CLAUDE.md primary module)
3. Link to existing UserRequest entity from FRS-4 context

