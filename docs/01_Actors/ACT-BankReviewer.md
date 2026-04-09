---
type: actor
id: ACT-BankReviewer
version: "1.0.0"
module: Request Detail View
milestone: null
status: draft
description: "Bank employee who reviews and approves or rejects user account requests"
source_frs: "[[FRS-UC-00008]]"
linked_capabilities: ["[[CAP-AccessRequestDetails]]"]
linked_commands: ["[[CMD-ViewRequestDetails]]"]
linked_flows: ["[[FLOW-ViewRequestDetail]]"]
---

# ACT-BankReviewer

Bank reviewers are financial institution employees responsible for evaluating user account creation requests submitted by companies. They review request details, assess compliance, and determine approval or rejection. Bank reviewers have broader visibility than company administrators—they can see all requests, not just their own organization's requests.

## Goals

| Goal | Trigger | Success Condition | Primary Flow |
|------|---------|-------------------|--------------|
| Review request details | Request notification or manual search | View complete request information | [[FLOW-ViewRequestDetail]] |
| Assess request validity | Request is pending review | Determine approval/rejection decision | [[FLOW-ViewRequestDetail]] |

## Permissions

- May issue [[CMD-ViewRequestDetails]] for any request in the system (no ownership restriction).
- May view request status, reviewer notes, rejection reasons, and submission timestamps.
- Cannot edit request details once submitted (read-only access).

## Constraints

- Requires bank employee authentication and valid role assignment (`bank_reviewer`).
- Access is audited and logged per regulatory requirements.
- Request details are visible only after submission is complete (no draft request viewing).
