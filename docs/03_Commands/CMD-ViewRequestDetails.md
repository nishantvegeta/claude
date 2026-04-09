---
type: command
id: CMD-ViewRequestDetails
version: "1.0.0"
module: Request Detail View
milestone: null
status: draft
description: "Retrieve and display complete details of a specific user request"
source_frs: "[[FRS-UC-00008]]"
linked_flows: ["[[FLOW-ViewRequestDetail]]"]
linked_entities: ["[[ENT-Request]]"]
linked_actors: ["[[ACT-CompanySuperAdmin]]", "[[ACT-BankReviewer]]"]
---

# CMD-ViewRequestDetails

CMD-ViewRequestDetails retrieves the full details of a user account request by request ID and renders them for authorized viewing. This command performs permission validation (ensuring the caller has access to view the specific request) and formats the request data for display in both customer portal and back-office views.

## Contract

**Input**

| Field | Type | Required | Validation |
|-------|------|----------|-----------|
| request_id | uuid | yes | Valid UUID; must exist in the system |

**Output**

| Field | Type | Notes |
|-------|------|-------|
| request | object | Complete ENT-Request object with all attributes |
| formatted_status | string | Human-readable status (e.g., "Pending Review", "Approved", "Rejected") |
| status_badge_color | string | CSS color code for status badge display |
| rejection_reason | string \| null | Populated only if status = "rejected" |
| access_allowed | boolean | True if caller has permission; false if unauthorized |
| error_message | string \| null | Populated if access_allowed = false |

## Conditions

**Preconditions:**
- Caller is authenticated and has a valid role (Company Super Admin or Bank Reviewer).
- Request ID exists in the system.
- Request has completed submission (status ≠ draft).

**Postconditions:**
- If authorized: Request data is retrieved and formatted for display. No state changes to the request.
- If unauthorized: Error message is returned; no request data is exposed.
- All access attempts are logged for audit purposes.

## Error Handling

| Scenario | HTTP Status | Error Code | Message |
|----------|------------|-----------|---------|
| Unauthorized access | 403 | ERR_UNAUTHORIZED | "You are not allowed to view this request" |
| Request not found | 404 | ERR_NOT_FOUND | "Request does not exist" |
| Invalid request ID format | 400 | ERR_INVALID_INPUT | "Invalid request ID format" |
| Caller not authenticated | 401 | ERR_UNAUTHENTICATED | "Authentication required" |
