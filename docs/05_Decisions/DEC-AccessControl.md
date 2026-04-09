---
type: decision
id: DEC-AccessControl
version: "1.0.0"
module: Request Detail View
milestone: null
status: draft
description: "Role-based access control for viewing request details—company admins see own requests only; bank reviewers see all"
source_frs: "[[FRS-UC-00008]]"
linked_entities: ["[[ENT-Request]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-BankReviewer]]"]
linked_flows: ["[[FLOW-ViewRequestDetail]]"]
---

# DEC-AccessControl

DEC-AccessControl establishes the authorization policy for viewing request details. The decision reflects a fundamental domain constraint: company administrators are restricted to viewing requests within their own organization, while bank reviewers—as institutional gatekeepers—can view all requests regardless of submitting organization. This asymmetry protects multi-tenant data isolation while enabling bank reviewers to fulfill their oversight role.

## Decision Statement

**Company Super Admins** are permitted to view request details only for requests submitted by their own organization. **Bank Reviewers** are permitted to view request details for all requests in the system, regardless of submitting organization.

## Rationale

- **Data isolation:** Companies should not have visibility into competitors' or other organizations' user account requests. Restricting company admins to their own organization's requests enforces data boundaries.
- **Bank reviewer oversight:** Bank reviewers operate at the system level and must evaluate all submissions uniformly. Unrestricted access enables fair and consistent review processes.
- **Regulatory compliance:** Role-based access control (RBAC) aligns with financial services compliance requirements (e.g., SOX, SOC2) for segregation of duties and audit trails.

## Enforcement

Access validation is performed in [[CMD-ViewRequestDetails]] before any request data is returned:

1. Authenticate the caller (verify valid session and role).
2. If caller is Company Super Admin:
   - Fetch request by ID.
   - Verify request's `company_id` matches caller's organization.
   - If mismatch, return 403 Forbidden with message "You are not allowed to view this request".
   - If match, proceed to render detail view.
3. If caller is Bank Reviewer:
   - Fetch request by ID.
   - No organization check required.
   - Proceed to render detail view.

## Alternative Considered

**Option:** Allow Company Super Admins to view all requests (including competitors' requests).
- **Rejected:** Violates multi-tenant isolation. Creates security and compliance risk.

**Option:** Restrict Bank Reviewers to requests from specific partner organizations.
- **Rejected:** Reduces bank's ability to conduct fair, uniform review. Conflicts with regulatory expectations for objective gatekeeping.

## Implications

- [[FLOW-ViewRequestDetail]] must invoke [[CMD-ViewRequestDetails]] with permission validation (see Shadow QA—Edge Case).
- [[ENT-Request]] frontmatter must include `company_id` for ownership tracking.
- All access denials are logged for audit compliance.
