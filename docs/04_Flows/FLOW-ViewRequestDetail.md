---
type: flow
id: FLOW-ViewRequestDetail
version: "1.0.0"
module: Request Detail View
milestone: null
status: draft
logic_gate: STRICT
description: "User navigates from request list to view full details of a specific request"
source_frs: "[[FRS-UC-00008]]"
linked_commands: ["[[CMD-ViewRequestDetails]]"]
linked_entities: ["[[ENT-Request]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-BankReviewer]]", "[[ACT-System]]"]
scenario_gap: false
---

# FLOW-ViewRequestDetail

FLOW-ViewRequestDetail orchestrates the user journey for viewing detailed information about a specific request. The flow begins when a user (either a company administrator viewing their own request or a bank reviewer viewing any request) clicks on a request from the tracking list. The system validates access permissions, retrieves request data, and displays a comprehensive detail view including status, review information, and rejection reason (if applicable).

## Sequence

1. **User navigates to request tracking list** — User opens the portal and navigates to the request tracking/history view. System displays list of accessible requests (filtered by ownership for company admins; all requests for bank reviewers).

2. **User clicks on a request row** — User selects a specific request from the list by clicking on the request row. The request ID is captured and passed to the next step.

3. **[[CMD-ViewRequestDetails]]** — System invokes the command to fetch and validate access. Requires min v1.0.0. Command validates that:
   - Caller is authenticated (Company Super Admin or Bank Reviewer role)
   - If Company Super Admin: request belongs to their organization
   - If Bank Reviewer: access is allowed (no org restriction)
   - Request exists and has completed submission

4. **System formats and renders request details** — System transforms the retrieved [[ENT-Request]] object into display format, applying color coding to the status badge and formatting timestamps for human readability. If status is "rejected", rejection reason is included.

5. **User views complete request information** — Detail view is displayed showing:
   - Requestor name, email, target role
   - Submission date
   - Current status with color-coded badge
   - Reviewer name (if reviewed)
   - Review date (if reviewed)
   - Rejection reason (if rejected)
   - Back button to return to list

6. **User navigates back to list** — User clicks back button or closes detail view, returning to the request tracking list.

Logic gate is **STRICT**: all steps must succeed without interruption. If permission validation fails at step 3, the flow halts and an error message is displayed (see Shadow QA—Error Path).

## Shadow QA

> Shadow QA in this section is the **single source of truth** for test scenarios.
> Feature Specs reference this section by wikilink. Do not duplicate these scenarios elsewhere.

### Happy Path

**Given** [[ACT-CompanySuperAdmin]] with a pending request in their organization, **when** [[CMD-ViewRequestDetails]] is invoked with the request ID, **then** the request detail view is rendered with all fields populated (name, email, role, submission date, current status with color badge, and empty rejection reason field). The user can read all information and click back to return to the list.

### Edge Case: Company Admin Viewing Another Org's Request

**Given** [[ACT-CompanySuperAdmin]] from Organization A with a request ID from Organization B, **when** [[CMD-ViewRequestDetails]] is invoked, **then** the command is rejected with error code `ERR_UNAUTHORIZED` and message "You are not allowed to view this request". The detail view is not rendered, and the user is returned to the list with an error notification.

### Edge Case: Bank Reviewer Viewing Any Request

**Given** [[ACT-BankReviewer]] with any valid request ID (regardless of organization), **when** [[CMD-ViewRequestDetails]] is invoked, **then** the request detail view is rendered with all fields including reviewer information and rejection reason (if applicable). No access restriction is applied.

### Edge Case: Request Not Found

**Given** [[ACT-CompanySuperAdmin]] or [[ACT-BankReviewer]] with a non-existent request ID, **when** [[CMD-ViewRequestDetails]] is invoked, **then** the command is rejected with error code `ERR_NOT_FOUND` and message "Request does not exist". The user is shown an error message and returned to the list.

### Fault Path: Unauthenticated Access

**Given** an unauthenticated user (not logged in), **when** the user attempts to access the request detail view, **then** the system redirects to login page. No request data is exposed. The detail view is not rendered.

### Happy Path: Rejected Request with Reason

**Given** [[ACT-BankReviewer]] viewing a request with status "rejected" and a rejection reason populated, **when** [[CMD-ViewRequestDetails]] is invoked, **then** the rejection reason is displayed prominently in the detail view alongside the red "Rejected" status badge. The user can see the complete rationale for the rejection.
