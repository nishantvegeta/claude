---
title: Knowledge Graph Index
description: Complete index of all DDD nodes in the wiki
generated: 2026-04-09T10:40:00Z
total_nodes: 8
---

# Knowledge Graph Index

**Last Updated:** 2026-04-09T11:50:00Z  
**Total Nodes:** 15  
**Status:** FEAT-RequestDetailView-001 compiled and auto-approved (BA role); ready for GitLab issue creation

## All Nodes by Type

### Actors (ACT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| ACT-CompanySuperAdmin | actor | Company administrator who manages requests | Task Management | draft | FRS-5 |
| ACT-System | actor | System actor for request processing | Task Management | draft | FRS-5 |
| ACT-BankReviewer | actor | Bank employee who reviews and approves requests | Request Detail View | draft | FRS-UC-00008 |

### Entities (ENT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| ENT-Request | entity | User request with approval lifecycle | Task Management | draft | FRS-5 |

### Commands (CMD-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| CMD-CancelRequest | command | Cancel pending request | Task Management | draft | FRS-5 |
| CMD-ViewRequestDetails | command | Retrieve and display request details | Request Detail View | draft | FRS-UC-00008 |

### Flows (FLOW-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| FLOW-RequestCancellation | flow | Request cancellation workflow | Task Management | draft | FRS-5 |
| FLOW-ViewRequestDetail | flow | View request detail user journey | Request Detail View | draft | FRS-UC-00008 |

### States (STATE-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| STATE-RequestLifecycle | state | Request status machine | Task Management | draft | FRS-5 |
| STATE-RequestStatus | state | Request approval status state machine | Request Detail View | draft | FRS-UC-00008 |

### Decisions (DEC-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| DEC-CancellationPolicy | decision | Cancellation is terminal | Task Management | draft | FRS-5 |
| DEC-AccessControl | decision | Role-based access control for request viewing | Request Detail View | draft | FRS-UC-00008 |

### Features (FEAT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| FEAT-TaskManagement-RequestCancellation | feature_spec | Request Cancellation | Task Management | implemented | FRS-5 |
| FEAT-RequestDetailView-001 | feature_spec | Request Detail View | Request Detail View | approved | FRS-UC-00008 |

### API Release Docs (APIDOC-)

| ID | Type | Title | Version | Status | Linked Features |
|----|------|-------|---------|--------|-----------------|
| APIDOC-v1.0.0 | api_release_doc | API Release — v1.0.0 | v1.0.0 | draft | FEAT-TaskManagement-RequestCancellation |

---

## Summary by Module

### Task Management

- **Actors:** 2
- **Entities:** 1
- **Commands:** 1
- **Flows:** 1
- **States:** 1
- **Decisions:** 1
- **Features:** 1
- **API Release Docs:** 1
- **Total:** 9 nodes

### Request Detail View

- **Actors:** 1 (ACT-BankReviewer)
- **Entities:** 0 (ENT-Request reused from Task Management)
- **Commands:** 1 (CMD-ViewRequestDetails)
- **Flows:** 1 (FLOW-ViewRequestDetail)
- **States:** 1 (STATE-RequestStatus)
- **Decisions:** 1 (DEC-AccessControl)
- **Features:** 1 (FEAT-RequestDetailView-001, approved)
- **Total:** 6 nodes (5 new + 1 FEAT)

---

## Traceability

All nodes created from:
- **FRS-5:** Request Cancellation (greenfield absorption, 9 nodes)
- **FRS-UC-00008:** Request Detail View (5 new nodes, 3 reused from FRS-5)

---

## Node Status Distribution

| Status | Count |
|--------|-------|
| draft | 13 |
| active | 0 |
| approved | 1 |
| implemented | 1 |
| deprecated | 0 |
| superseded | 0 |

DDD nodes from both FRS-5 and FRS-UC-00008 (ACT-, ENT-, CMD-, FLOW-, STATE-, DEC-) are in `draft` status pending BA review. FEAT-RequestDetailView-001 is in `approved` status (BA auto-approved). FEAT-TaskManagement-RequestCancellation is in `implemented` status. APIDOC-v1.0.0 is in `draft` status pending publication review.

