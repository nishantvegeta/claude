---
title: Knowledge Graph Index
description: Complete index of all DDD nodes in the wiki
generated: 2026-04-09T10:40:00Z
total_nodes: 8
---

# Knowledge Graph Index

**Last Updated:** 2026-04-09T10:45:00Z  
**Total Nodes:** 9  
**Status:** APIDOC-v1.0.0 generated (draft, pending publication)

## All Nodes by Type

### Actors (ACT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| ACT-CompanySuperAdmin | actor | Company administrator who manages requests | Task Management | draft | FRS-5 |
| ACT-System | actor | System actor for request processing | Task Management | draft | FRS-5 |

### Entities (ENT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| ENT-Request | entity | User request with approval lifecycle | Task Management | draft | FRS-5 |

### Commands (CMD-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| CMD-CancelRequest | command | Cancel pending request | Task Management | draft | FRS-5 |

### Flows (FLOW-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| FLOW-RequestCancellation | flow | Request cancellation workflow | Task Management | draft | FRS-5 |

### States (STATE-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| STATE-RequestLifecycle | state | Request status machine | Task Management | draft | FRS-5 |

### Decisions (DEC-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| DEC-CancellationPolicy | decision | Cancellation is terminal | Task Management | draft | FRS-5 |

### Features (FEAT-)

| ID | Type | Title | Module | Status | Source FRS |
|----|------|-------|--------|--------|-----------|
| FEAT-TaskManagement-RequestCancellation | feature_spec | Request Cancellation | Task Management | implemented | FRS-5 |

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

---

## Traceability

All nodes created from:
- **FRS-5:** Request Cancellation (greenfield absorption)

---

## Node Status Distribution

| Status | Count |
|--------|-------|
| draft | 8 |
| active | 0 |
| approved | 0 |
| implemented | 1 |
| deprecated | 0 |
| superseded | 0 |

DDD nodes (ACT-, ENT-, CMD-, FLOW-, STATE-, DEC-) are in `draft` status pending BA review. FEAT-TaskManagement-RequestCancellation is in `implemented` status. APIDOC-v1.0.0 is in `draft` status pending publication review.

