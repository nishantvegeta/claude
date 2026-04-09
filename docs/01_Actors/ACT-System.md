---
type: actor
id: ACT-System
version: "1.0.0"
module: Task Management
milestone: null
status: draft
description: "System actor that processes request status updates and cancellations"
source_frs: "[[FRS-5]]"
linked_capabilities: ["[[CAP-RequestManagement]]"]
linked_commands: ["[[CMD-CancelRequest]]"]
linked_flows: ["[[FLOW-RequestCancellation]]"]
---

# ACT-System

System is an automated actor that handles synchronous processing of cancellation requests. It validates request ownership and status, updates the request entity state, records timestamps, and persists changes to storage. No human intervention required; operates asynchronously and deterministically.

## Goals

| Goal | Trigger | Success Condition | Primary Flow |
|------|---------|-------------------|--------------|
| Process cancellation | User submits cancellation confirmation | Request status updated → `cancelled`, timestamp recorded | [[FLOW-RequestCancellation]] |

## Permissions

- May issue [[CMD-CancelRequest]] when company admin has authorized the action.
- System processes all requests without role-based restrictions.
- May update ENT-Request status and metadata fields.

## Constraints

- Cannot cancel requests that have already been processed (status != `pending`)
- Must validate request ownership before proceeding
- Must log all cancellation actions for audit compliance
- Updates are atomic—either fully committed or fully rolled back

