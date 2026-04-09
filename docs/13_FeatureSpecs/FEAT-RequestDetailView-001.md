---
type: feature_spec
id: FEAT-RequestDetailView-001
version: "1.0.0"
module: Request Detail View
milestone: null
status: approved
description: "Display complete request details with role-based access control for company admins and bank reviewers"
source_frs: ["[[FRS-UC-00008]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-BankReviewer]]", "[[ACT-System]]"]
linked_entities: ["[[ENT-Request]]"]
linked_commands: ["[[CMD-ViewRequestDetails]]"]
linked_flows: ["[[FLOW-ViewRequestDetail]]"]
linked_states: ["[[STATE-RequestStatus]]"]
linked_decisions: ["[[DEC-AccessControl]]"]
linked_integrations: []
gitlab_issue: "http://gitlab.local:8080/root/artifacts/-/issues/9"
covered_by_apidoc: ""
rejected_reason: ""
superseded_by: ""
auto_approved_by: BA
---

# FEAT-RequestDetailView-001 — Request Detail View

## Summary

This feature enables users to view detailed information about user account requests. Company Super Admins can view requests from their own organization; Bank Reviewers can view all requests in the system. The feature enforces role-based access control, displays request status with visual indicators, and shows reviewer feedback (approval or rejection reason) when applicable.

---

## Tasks

### Task 1 — View Request Details  `[FRS-UC-00008]`

**Source:** [[FRS-UC-00008]]  
**Depends on:** —  
**Nodes:** [[ENT-Request]], [[CMD-ViewRequestDetails]], [[FLOW-ViewRequestDetail]]

This task builds the core request detail retrieval and display capability. When a user clicks on a request from the tracking list, the system validates access permissions (checking ownership for company admins, allowing all for bank reviewers), retrieves the complete request record, formats it for display, and renders the detail view with all fields populated.

**Technical Scope**

- Request detail retrieval endpoint with permission validation
- Response formatting and field transformation for UI display
- Status badge color mapping (pending → amber, approved → green, rejected → red)
- Error handling for 401 (unauthorized) and 404 (not found) scenarios
- Access audit logging

**Acceptance Criteria**

- [ ] Company Super Admin can view requests from their own organization only
- [ ] Bank Reviewer can view any request in the system without organization restriction
- [ ] Unauthorized access returns 403 error without exposing request data
- [ ] Request not found returns 404 error
- [ ] All request fields (name, email, role, status, reviewer info, rejection reason) display correctly
- [ ] Status badge displays with correct color coding
- [ ] Rejection reason displays only when status is "rejected"
- [ ] All access attempts are logged for audit compliance

**Shadow QA**

→ [[FLOW-ViewRequestDetail#Shadow-QA]]

---

### Task 2 — Enforce Role-Based Access Control  `[FRS-UC-00008]`

**Source:** [[FRS-UC-00008]]  
**Depends on:** Task 1  
**Nodes:** [[DEC-AccessControl]], [[ACT-CompanySuperAdmin]], [[ACT-BankReviewer]], [[STATE-RequestStatus]]

This task implements the authorization policy defined in [[DEC-AccessControl]]. The decision enforces asymmetric access: company administrators are restricted to their own organization's requests (protecting multi-tenant data isolation), while bank reviewers have unrestricted system-wide access (enabling fair and consistent review). Permission validation occurs before any request data is retrieved or displayed.

**Technical Scope**

- Permission validation logic (organization ownership check for admins)
- Caller authentication and role resolution
- Request ownership verification
- Error response generation for denied access
- Audit trail logging with caller identity and request ID

**Acceptance Criteria**

- [ ] Permission check happens before data retrieval
- [ ] Company admin viewing own org's request succeeds
- [ ] Company admin viewing another org's request returns 403
- [ ] Bank reviewer viewing any request succeeds
- [ ] Unauthenticated access triggers redirect to login
- [ ] All permission denials are logged with caller, request ID, and timestamp
- [ ] Logs meet SOX/SOC2 compliance requirements

**Shadow QA**

→ [[FLOW-ViewRequestDetail#Shadow-QA]]

---

## Performance Contracts

| Operation | Target | Measurement Point | Source |
|-----------|--------|-------------------|--------|
| (None defined) | — | — | (No SLAs in scope) |

## Out of Scope

- Editing request data from detail view
- Approval/rejection from detail view (performed separately by bank reviewers)
- Request filtering or search
- Bulk operations on requests
- Mobile-specific optimizations (desktop portal focus)

## Open Questions

None at this time. All requirements from FRS-UC-00008 have been addressed by the absorbed DDD nodes.

---

## Traceability

| Node Type | Node ID | Relationship |
|-----------|---------|--------------|
| Actors | ACT-CompanySuperAdmin | User role initiating view request |
| Actors | ACT-BankReviewer | User role initiating view request |
| Actors | ACT-System | System orchestrating retrieval and display |
| Entities | ENT-Request | Core domain entity being viewed |
| Commands | CMD-ViewRequestDetails | API action triggered by user click |
| Flows | FLOW-ViewRequestDetail | User journey from list to detail view |
| States | STATE-RequestStatus | Request status machine defining display states |
| Decisions | DEC-AccessControl | Authorization policy governing visibility |

