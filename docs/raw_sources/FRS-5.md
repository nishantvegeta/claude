---
id: FRS-5
iid: 5
milestone: null
actor: Company Super Admin
goal: "Allow Company Super Admins to cancel pending user requests before they are reviewed"
preconditions:
  - User is authenticated as Company Super Admin
  - Request exists and belongs to user
  - Request status is "pending"
success_outcomes:
  - Request status updated to "cancelled"
  - Cancellation timestamp recorded
  - User sees confirmation message
  - Request no longer appears in actionable list
failure_outcomes:
  - User is not request owner → "You cannot cancel this request"
  - Request already processed → "Only pending requests can be cancelled"
  - Concurrent modification → error logged
source_issue: http://gitlab.local:8080/root/artifacts/-/issues/5
source_type: frs
intended_nodes:
  - ACT-CompanySuperAdmin
  - ACT-System
  - ENT-Request
  - CMD-CancelRequest
  - FLOW-RequestCancellation
  - STATE-RequestLifecycle
  - DEC-CancellationPolicy
monolith_risk: false
fetched_at: 2026-04-09
---

# Functional Requirements Specification: Request Cancellation

**Feature:** Request Cancellation  
**Complexity Tier:** Low  
**Portals:** Customer Portal  
**Date:** 2026-04-09

---

## 1. Overview

### 1.1 Objective

Allow Company Super Admins to cancel pending user requests before they are reviewed.

### 1.2 Actors

- **Company Super Admin**: Cancels own requests
- **System**: Updates request status

---

## 2. Scope

### In-Scope

- Cancel pending requests
- Update request status to "cancelled"
- Reflect cancellation in tracking list

### Out-of-Scope

- Cancelling approved/rejected requests
- Undo cancellation

---

## 3. Functional Requirements

### FR-001: Cancel Request

- **Trigger**: User clicks "Cancel" on a pending request
- **Inputs**: Request ID
- **Processing:**
  1. Validate request belongs to user
  2. Ensure status = "pending"
  3. Update status → "cancelled"
  4. Save timestamp
- **Output**: Confirmation message

---

## 4. UI Requirements

- Cancel button visible only for pending requests
- Confirmation modal: "Are you sure you want to cancel this request?"
- Status badge updates to "Cancelled" (gray)

---

## 5. Data Model

| Field | Type | Description |
|-------|------|-------------|
| Request ID | string | Unique identifier |
| Status | enum | pending / approved / rejected / cancelled |
| Cancelled Date | datetime | Timestamp of cancellation |

---

## 6. Workflow

1. User views request list
2. Clicks "Cancel" on pending request
3. Confirms action
4. System updates status to "cancelled"
5. Request list refreshes

---

## 7. Validation & Errors

| Scenario | Message |
|----------|---------|
| Not owner | "You cannot cancel this request" |
| Already processed | "Only pending requests can be cancelled" |

---

## 8. Security

- Only request owner can cancel
- Role-based access enforced
- Action logged for audit

---

## 9. Acceptance Criteria

- User can cancel only pending requests
- Cancelled requests are no longer actionable
- Status updates correctly in UI
- Unauthorized users cannot cancel requests

---

## 10. Assumptions

- Request tracking list already implemented
- User authentication is available
- Audit logging system exists

