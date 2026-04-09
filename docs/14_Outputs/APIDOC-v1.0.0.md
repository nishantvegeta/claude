---
type: api_release_doc
id: APIDOC-v1.0.0
version: "v1.0.0"
status: draft
audience: external
milestone: null
description: "API release for Request Cancellation feature (Task Management v1.0)"
linked_features: ["[[FEAT-TaskManagement-RequestCancellation]]"]
covered_by_apidoc: ""
deprecated_by: ""
---

# API Release — v1.0.0 (External)

## Overview

This API release documents the Request Cancellation feature for the Task Management module. It enables Company Super Admins to cancel pending user account requests before they are reviewed by the bank. The API is designed for external integrations with the customer portal and internal bank review systems.

**Included Features:**
- [[FEAT-TaskManagement-RequestCancellation]] — Request Cancellation

**Audience:** External developers integrating with the customer portal; internal bank review system administrators.

## Authentication

All endpoints require Bearer token authentication (OAuth 2.0 or JWT).

**Token Requirements:**
- Header: `Authorization: Bearer {token}`
- Token must be issued to a Company Super Admin user
- Token scope: `requests:write` (required for cancellation operations)
- Token expiration: 1 hour (refresh token available)

**Rate Limits:**
- 1000 requests per minute per authenticated user
- 429 Too Many Requests response includes `Retry-After` header

## Base URL

**Production:** `https://api.example.com/v1/`  
**Staging:** `https://staging-api.example.com/v1/`  
**Development:** `https://dev-api.example.com/v1/`

## Endpoints

### Resource: Request Cancellation

| Method | Path | Description | Command |
|--------|------|-------------|---------|
| POST | /requests/{request_id}/cancel | Cancel pending request | [[CMD-CancelRequest]] |

#### POST /requests/{request_id}/cancel

Cancel a pending user account request.

**Parameters:**

- Path: `request_id` — UUID, required. The ID of the request to cancel.
- Body: JSON object with fields below

**Request Body:**

```json
{
  "company_id": "uuid",
  "reason": "string (optional, max 500 chars)"
}
```

**Responses:**

- `200 OK` — Request cancelled successfully
- `400 Bad Request` — Invalid request (malformed JSON, missing fields)
- `401 Unauthorized` — Missing or invalid authentication token
- `403 Forbidden` — User lacks `requests:write` scope
- `404 Not Found` — Request ID does not exist
- `409 Conflict` — Request status is not `pending` or already cancelled
- `429 Too Many Requests` — Rate limit exceeded
- `500 Internal Server Error` — Unexpected server error

**Success Response (200 OK):**

```json
{
  "request_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "cancelled",
  "cancelled_at": "2026-04-09T12:34:56Z",
  "confirmation_message": "Request successfully cancelled"
}
```

**Error Response (4xx/5xx):**

```json
{
  "error": {
    "code": "INVALID_STATE",
    "message": "Only pending requests can be cancelled",
    "details": {
      "request_id": "550e8400-e29b-41d4-a716-446655440000",
      "current_status": "approved"
    }
  }
}
```

**Example cURL:**

```bash
curl -X POST https://api.example.com/v1/requests/550e8400-e29b-41d4-a716-446655440000/cancel \
  -H "Authorization: Bearer eyJhbGc..." \
  -H "Content-Type: application/json" \
  -d '{
    "company_id": "660e8400-e29b-41d4-a716-446655440001",
    "reason": "No longer needed"
  }'
```

## Data Models (Schemas)

**Request Object**

```json
{
  "id": "uuid",
  "company_id": "uuid",
  "owner_id": "uuid",
  "status": "enum: pending|approved|rejected|cancelled",
  "created_at": "ISO8601",
  "cancelled_at": "ISO8601 (null if not cancelled)",
  "reviewed_at": "ISO8601 (null if not reviewed)"
}
```

**Error Response**

```json
{
  "error": {
    "code": "string",
    "message": "string",
    "details": "object (optional)"
  }
}
```

## Error Codes

| Code | Meaning | Resolution | HTTP Status |
|------|---------|------------|-------------|
| `INVALID_REQUEST` | Malformed JSON or missing required fields | Check request body format | 400 |
| `INVALID_STATE` | Request status is not `pending` | Only pending requests can be cancelled | 409 |
| `REQUEST_NOT_FOUND` | Request ID does not exist | Verify request ID is correct | 404 |
| `UNAUTHORIZED` | Request belongs to different organization | Verify company_id matches request owner | 403 |
| `ALREADY_CANCELLED` | Request is already cancelled | Treat as success (idempotent) | 200 |
| `INVALID_TOKEN` | Authentication token invalid or expired | Re-authenticate and obtain new token | 401 |
| `INSUFFICIENT_SCOPE` | Token lacks required scope | Obtain token with `requests:write` scope | 403 |
| `RATE_LIMITED` | Too many requests from this user | Wait and retry after `Retry-After` seconds | 429 |
| `INTERNAL_ERROR` | Unexpected server error | Retry with exponential backoff | 500 |

## Versioning Policy

- **Semantic versioning:** Major.Minor.Patch (MAJOR for breaking changes, MINOR for additive changes, PATCH for bugfixes)
- **Backward compatibility:** Guaranteed within same major version. Breaking changes → major version bump.
- **Deprecation timeline:** When an endpoint is deprecated, it remains available for at least 6 months with a `Deprecation` header warning. Then removal in next major version.

## Breaking Changes in This Release

**None in this release.** This is the initial v1.0.0 release.

## Deprecations

**None in this release.**

## Changelog

For feature-level narrative, see: [[CHGLOG-v1.0.0-internal]] (internal changelog).

---

## Related Documents

- **Feature Spec:** [[FEAT-TaskManagement-RequestCancellation]]
- **Command:** [[CMD-CancelRequest]]
- **Flow:** [[FLOW-RequestCancellation]]
- **Entity:** [[ENT-Request]]
