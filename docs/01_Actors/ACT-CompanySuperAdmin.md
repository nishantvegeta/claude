---
type: actor
id: ACT-CompanySuperAdmin
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "Company administrator who initiates and manages user requests and cancellations"
source_frs: "[[FRS-5]]"
linked_capabilities: ["[[CAP-RequestManagement]]"]
linked_commands: ["[[CMD-CancelRequest]]"]
linked_flows: ["[[FLOW-RequestCancellation]]"]
---

# ACT-CompanySuperAdmin

Company Super Admin is a human actor authorized to manage user requests within their organization's portal. They can submit, view, and cancel pending user requests before bank review, providing control over request lifecycle prior to approval processing. Operates within the Task Management bounded context.

## Goals

| Goal | Trigger | Success Condition | Primary Flow |
|------|---------|-------------------|--------------|
| Cancel pending request | User clicks "Cancel" button on pending request | Request status → `cancelled` | [[FLOW-RequestCancellation]] |

## Permissions

- May issue [[CMD-CancelRequest]] when request belongs to their organization and status is `pending`.
- May not issue [[CMD-CancelRequest]] on requests with status `approved`, `rejected`, or `cancelled`.
- May not cancel requests belonging to other organizations.

## Constraints

- Must be authenticated to customer portal with valid session
- Can only cancel own organization's requests (role-based access control)
- Cannot cancel after bank has begun review
- Cancellation action is logged for audit trail

