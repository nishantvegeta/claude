---
status: staged
ingest_timestamp: 2026-04-09T10:20:00Z
source_type: frs
intended_nodes:
  - ACT-CompanySuperAdmin
  - ACT-BankReviewer
  - ACT-System
  - ENT-UserRequest
  - ENT-User
  - CMD-SubmitUserRequest
  - CMD-ApproveRequest
  - CMD-RejectRequest
  - CMD-GenerateUserIdentity
  - FLOW-RequestSubmission
  - FLOW-RequestApproval
  - FLOW-UserIdentityGeneration
pre_scan_conflict: true
pre_scan_conflict_note: |
  Monolith constraint violation detected (4 signals): Multiple actors (3), Multiple command triggers (4+), 
  Independent outcome sets, Disjoint state machines. Exception approved via CNF-002 to proceed with compilation. 
  DDD graph will include all 13 candidate nodes under single module context.
monolith_exception_cnf: CNF-002
candidate_count: 13
---

# Staging Entry: FRS-4

**Source:** Issue #4 from root/artifacts  
**Fetched:** 2026-04-09  
**Status:** STAGED (ready for ABSORB)

## Candidate Nodes

| Type | ID | Name |
|------|-----|------|
| ACT- | ACT-001 | Company Super Admin |
| ACT- | ACT-002 | Bank Reviewer |
| ACT- | ACT-003 | System |
| ENT- | ENT-001 | UserRequest |
| ENT- | ENT-002 | User |
| CMD- | CMD-001 | SubmitUserRequest |
| CMD- | CMD-002 | ApproveRequest |
| CMD- | CMD-003 | RejectRequest |
| CMD- | CMD-004 | GenerateUserIdentity |
| FLOW- | FLOW-001 | RequestSubmission |
| FLOW- | FLOW-002 | RequestApproval |
| FLOW- | FLOW-003 | UserIdentityGeneration |
| DEC- | DEC-001 | Monolith Exception Approval |

## Monolith Exception

**CNF:** CNF-002 (pending BA approval)  
**Reason:** 4-signal monolith constraint violation  
**Impact:** All 13 nodes will be compiled into single module context  
**Mitigation:** LINT will flag decomposition debt; BA must approve exception before ABSORB proceeds

## Pre-Scan Conflicts

✓ No cross-existing node contradictions detected  
⚠️ Monolith exception pending BA approval (blocking ABSORB)

## Next Steps

1. BA reviews and approves CNF-002 exception
2. Set `status: resolved` on CNF-002
3. Run `absorb 4` to compile candidates → DDD nodes
4. Monitor LINT for decomposition debt warnings

