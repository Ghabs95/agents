# Floor Plan Editor - API Contracts

**Document Version:** 1.0  
**Date:** 2026-02-15  
**Owner:** @Architect  
**Related:** [ADR-001](./ADR-001-floor-plan-editor-architecture.md), [Data Models](./floor-plan-data-models.md)

---

## Overview

This document defines the RESTful API contracts for floor plan CRUD operations between `casit-app` (Flutter) and `casit-be` (Python Flask).

**Base URL:** `https://api.caseitalia.com/api/v1`

---

## 1. Floor Plan Endpoints

### 1.1 Create Floor Plan

**Endpoint:** `POST /properties/{property_id}/floor_plans`

**Description:** Create a new floor plan for a property.

**Request Headers:**
```http
Content-Type: application/json
Authorization: Bearer {jwt_token}
```

**Path Parameters:**
- `property_id` (string, required): UUID of the property

**Request Body:**
```json
{
  "name": "Ground Floor",
  "rooms": [],
  "corners": [],
  "doors": [],
  "windows": [],
  "metadata": {
    "unit_system": "metric",
    "scale": 1.0
  }
}
```

**Response (201 Created):**
```json
{
  "id": "fp_abc123xyz",
  "property_id": "prop_789xyz",
  "name": "Ground Floor",
  "rooms": [],
  "corners": [],
  "doors": [],
  "windows": [],
  "metadata": {
    "unit_system": "metric",
    "scale": 1.0
  },
  "created_at": "2026-02-15T14:30:00Z",
  "updated_at": "2026-02-15T14:30:00Z",
  "svg_url": null
}
```

**Error Responses:**
- `400 Bad Request`: Invalid request body or validation failure
- `401 Unauthorized`: Missing or invalid JWT token
- `404 Not Found`: Property does not exist
- `409 Conflict`: Floor plan already exists for this property

**Example Error (400):**
```json
{
  "error": "validation_error",
  "message": "Room name is required",
  "details": {
    "field": "rooms[0].name",
    "constraint": "not_empty"
  }
}
```

---

### 1.2 Get Floor Plan by ID

**Endpoint:** `GET /properties/{property_id}/floor_plans/{floor_plan_id}`

**Description:** Retrieve a specific floor plan.

**Request Headers:**
```http
Authorization: Bearer {jwt_token}
```

**Path Parameters:**
- `property_id` (string, required)
- `floor_plan_id` (string, required)

**Response (200 OK):**
```json
{
  "id": "fp_abc123xyz",
  "property_id": "prop_789xyz",
  "name": "Ground Floor",
  "rooms": [
    {
      "id": "room_001",
      "name": "Soggiorno",
      "type": "livingRoom",
      "vertices": [{"x": 50, "y": 50}, {"x": 250, "y": 50}],
      "area": 30.0
    }
  ],
  "corners": [],
  "doors": [],
  "windows": [],
  "metadata": {},
  "created_at": "2026-02-15T14:30:00Z",
  "updated_at": "2026-02-15T15:00:00Z",
  "svg_url": "https://firebase.storage/floor_plans/fp_abc123xyz.svg"
}
```

**Error Responses:**
- `401 Unauthorized`
- `404 Not Found`: Floor plan does not exist

---

### 1.3 List Floor Plans for Property

**Endpoint:** `GET /properties/{property_id}/floor_plans`

**Description:** Retrieve all floor plans for a property (supports multi-floor buildings).

**Request Headers:**
```http
Authorization: Bearer {jwt_token}
```

**Path Parameters:**
- `property_id` (string, required)

**Query Parameters:**
- `page` (integer, optional, default: 1): Page number for pagination
- `per_page` (integer, optional, default: 20, max: 100): Items per page

**Response (200 OK):**
```json
{
  "floor_plans": [
    {
      "id": "fp_ground_001",
      "property_id": "prop_789xyz",
      "name": "Ground Floor",
      "metadata": {"floor_level": 0},
      "created_at": "2026-02-15T14:30:00Z",
      "updated_at": "2026-02-15T15:00:00Z",
      "svg_url": "https://firebase.storage/..."
    },
    {
      "id": "fp_first_002",
      "property_id": "prop_789xyz",
      "name": "First Floor",
      "metadata": {"floor_level": 1},
      "created_at": "2026-02-15T14:35:00Z",
      "updated_at": "2026-02-15T14:35:00Z",
      "svg_url": null
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 2,
    "total_pages": 1
  }
}
```

**Error Responses:**
- `401 Unauthorized`
- `404 Not Found`: Property does not exist

---

### 1.4 Update Floor Plan

**Endpoint:** `PUT /properties/{property_id}/floor_plans/{floor_plan_id}`

**Description:** Update an existing floor plan (replaces entire floor plan data).

**Request Headers:**
```http
Content-Type: application/json
Authorization: Bearer {jwt_token}
```

**Path Parameters:**
- `property_id` (string, required)
- `floor_plan_id` (string, required)

**Request Body:**
```json
{
  "name": "Ground Floor - Updated",
  "rooms": [
    {
      "id": "room_001",
      "name": "Living Room",
      "type": "livingRoom",
      "vertices": [{"x": 50, "y": 50}, {"x": 300, "y": 50}, {"x": 300, "y": 200}, {"x": 50, "y": 200}],
      "rotation": 0,
      "style": {
        "fill_color": "#E3F2FD",
        "stroke_color": "#1976D2",
        "stroke_width": 2.0,
        "opacity": 0.8
      },
      "door_ids": ["door_001"],
      "window_ids": ["window_001"],
      "corner_ids": ["corner_001", "corner_002"],
      "area": 37.5
    }
  ],
  "corners": [
    {
      "id": "corner_001",
      "floor_plan_id": "fp_abc123xyz",
      "position": {"x": 120, "y": 50},
      "is_auto_generated": true,
      "parent_opening_id": "door_001",
      "type": "openingEdge",
      "value": 30.0,
      "unit": "cm"
    },
    {
      "id": "corner_002",
      "floor_plan_id": "fp_abc123xyz",
      "position": {"x": 180, "y": 50},
      "is_auto_generated": true,
      "parent_opening_id": "door_001",
      "type": "openingEdge",
      "value": 30.0,
      "unit": "cm"
    }
  ],
  "doors": [
    {
      "id": "door_001",
      "floor_plan_id": "fp_abc123xyz",
      "room_id": "room_001",
      "position": {"x": 150, "y": 50},
      "width": 0.9,
      "rotation": 0,
      "type": "single",
      "opens_inward": true,
      "connected_corner_ids": ["corner_001", "corner_002"]
    }
  ],
  "windows": [
    {
      "id": "window_001",
      "floor_plan_id": "fp_abc123xyz",
      "room_id": "room_001",
      "position": {"x": 50, "y": 100},
      "width": 1.2,
      "height": 1.5,
      "rotation": 90,
      "type": "standard",
      "connected_corner_ids": []
    }
  ],
  "metadata": {
    "unit_system": "metric",
    "scale": 1.0,
    "version": 2
  },
  "svg_url": "https://firebase.storage/floor_plans/fp_abc123xyz_v2.svg"
}
```

**Response (200 OK):**
```json
{
  "id": "fp_abc123xyz",
  "property_id": "prop_789xyz",
  "name": "Ground Floor - Updated",
  "rooms": [...],
  "corners": [...],
  "doors": [...],
  "windows": [...],
  "metadata": {...},
  "created_at": "2026-02-15T14:30:00Z",
  "updated_at": "2026-02-15T16:00:00Z",
  "svg_url": "https://firebase.storage/floor_plans/fp_abc123xyz_v2.svg"
}
```

**Error Responses:**
- `400 Bad Request`: Validation error
- `401 Unauthorized`
- `404 Not Found`: Floor plan does not exist
- `409 Conflict`: Concurrent update conflict (use optimistic locking)

---

### 1.5 Partial Update Floor Plan (PATCH)

**Endpoint:** `PATCH /properties/{property_id}/floor_plans/{floor_plan_id}`

**Description:** Partially update floor plan (only specified fields are updated).

**Request Headers:**
```http
Content-Type: application/json
Authorization: Bearer {jwt_token}
```

**Request Body (Example - Update only name and SVG URL):**
```json
{
  "name": "Piano Terra",
  "svg_url": "https://firebase.storage/floor_plans/fp_abc123xyz_v3.svg"
}
```

**Response (200 OK):**
```json
{
  "id": "fp_abc123xyz",
  "property_id": "prop_789xyz",
  "name": "Piano Terra",
  "rooms": [...],  // Unchanged
  "corners": [...],  // Unchanged
  "doors": [...],  // Unchanged
  "windows": [...],  // Unchanged
  "metadata": {...},  // Unchanged
  "created_at": "2026-02-15T14:30:00Z",
  "updated_at": "2026-02-15T16:15:00Z",
  "svg_url": "https://firebase.storage/floor_plans/fp_abc123xyz_v3.svg"
}
```

**Error Responses:**
- `400 Bad Request`
- `401 Unauthorized`
- `404 Not Found`

---

### 1.6 Delete Floor Plan

**Endpoint:** `DELETE /properties/{property_id}/floor_plans/{floor_plan_id}`

**Description:** Permanently delete a floor plan.

**Request Headers:**
```http
Authorization: Bearer {jwt_token}
```

**Response (204 No Content):**
```
(Empty body)
```

**Error Responses:**
- `401 Unauthorized`
- `404 Not Found`
- `403 Forbidden`: User does not have permission to delete

---

## 2. Firebase Storage Integration

### 2.1 Upload SVG Export

**Flow:**
1. Flutter app exports floor plan as SVG using `floor_plan_editor` package
2. App uploads SVG to Firebase Storage using Firebase SDK
3. App receives Firebase Storage URL
4. App sends `PATCH` request to update `svg_url` field

**Firebase Storage Path:**
```
/floor_plans/{property_id}/{floor_plan_id}.svg
```

**Example Firebase Upload (Dart):**
```dart
Future<String> uploadFloorPlanSvg(String propertyId, String floorPlanId, String svgContent) async {
  final storageRef = FirebaseStorage.instance
      .ref()
      .child('floor_plans/$propertyId/$floorPlanId.svg');
  
  final uploadTask = storageRef.putString(
    svgContent,
    metadata: SettableMetadata(contentType: 'image/svg+xml'),
  );
  
  final snapshot = await uploadTask;
  final downloadUrl = await snapshot.ref.getDownloadURL();
  return downloadUrl;
}
```

---

## 3. Error Handling

### 3.1 Standard Error Response Format

```json
{
  "error": "error_code",
  "message": "Human-readable error message",
  "details": {
    "field": "path.to.field",
    "constraint": "validation_rule_name"
  },
  "timestamp": "2026-02-15T16:30:00Z",
  "request_id": "req_xyz789"
}
```

### 3.2 Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `validation_error` | 400 | Request body validation failed |
| `unauthorized` | 401 | Missing or invalid JWT token |
| `forbidden` | 403 | User lacks permission for this action |
| `not_found` | 404 | Resource does not exist |
| `conflict` | 409 | Concurrent update or duplicate resource |
| `internal_error` | 500 | Server-side error (logged for investigation) |

---

## 4. Authentication & Authorization

### 4.1 JWT Token Requirements

**Header:**
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Token Payload (Example):**
```json
{
  "user_id": "user_456def",
  "email": "surveyor@caseitalia.com",
  "roles": ["surveyor", "property_editor"],
  "iat": 1707999000,
  "exp": 1708085400
}
```

### 4.2 Authorization Rules

| Endpoint | Required Role | Notes |
|----------|---------------|-------|
| `POST /floor_plans` | `property_editor` or `surveyor` | Must own the property or have surveyor access |
| `GET /floor_plans/:id` | `property_viewer` or `property_editor` | Read-only access |
| `PUT /floor_plans/:id` | `property_editor` or `surveyor` | Must own the property |
| `DELETE /floor_plans/:id` | `property_owner` or `admin` | Restricted to owners only |

---

## 5. Rate Limiting

**Limits:**
- **Authenticated Users:** 100 requests per minute per user
- **Unauthenticated:** 10 requests per minute per IP

**Response Headers:**
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1707999600
```

**Rate Limit Exceeded (429 Too Many Requests):**
```json
{
  "error": "rate_limit_exceeded",
  "message": "Too many requests. Please wait 42 seconds.",
  "retry_after": 42
}
```

---

## 6. Versioning Strategy

**Current Version:** `v1`

**Version Selection:**
- **URL Path:** `/api/v1/properties/{id}/floor_plans` (Recommended)
- **Header:** `Accept: application/vnd.caseitalia.v1+json` (Alternative)

**Deprecation Policy:**
- v1 will be supported for **minimum 12 months** after v2 release
- Breaking changes require major version bump (v2, v3, etc.)
- Deprecation notice sent 6 months before sunset

---

## 7. Webhooks (Future Enhancement)

**Planned Events:**
- `floor_plan.created`
- `floor_plan.updated`
- `floor_plan.deleted`

**Webhook Payload Example:**
```json
{
  "event": "floor_plan.updated",
  "timestamp": "2026-02-15T16:45:00Z",
  "data": {
    "floor_plan_id": "fp_abc123xyz",
    "property_id": "prop_789xyz",
    "updated_by": "user_456def",
    "changes": ["rooms", "doors"]
  }
}
```

---

## 8. Testing

### 8.1 Contract Tests (Consumer-Driven)

**Tools:** Pact (Flutter → Python contract verification)

**Example Pact Contract:**
```dart
// test/pact/floor_plan_api_contract_test.dart
await pact
    .given('a property exists with id prop_789xyz')
    .uponReceiving('a request to create a floor plan')
    .withRequest(
      method: 'POST',
      path: '/api/v1/properties/prop_789xyz/floor_plans',
      headers: {'Content-Type': 'application/json'},
      body: {
        'name': 'Ground Floor',
        'rooms': [],
      },
    )
    .willRespondWith(
      status: 201,
      body: {
        'id': Matchers.somethingLike('fp_abc123'),
        'property_id': 'prop_789xyz',
        'name': 'Ground Floor',
      },
    );
```

### 8.2 API Mock Server (Development)

**Tool:** Mockoon or json-server

**Mock Data:** See `floor-plan-data-models.md` Section 4.1 for example payloads

---

## 9. Performance Requirements

### 9.1 Response Time SLAs

| Endpoint | Target Latency (p95) | Max Latency (p99) |
|----------|----------------------|-------------------|
| `GET /floor_plans/:id` | < 100ms | < 300ms |
| `POST /floor_plans` | < 200ms | < 500ms |
| `PUT /floor_plans/:id` | < 200ms | < 500ms |
| `DELETE /floor_plans/:id` | < 150ms | < 400ms |

### 9.2 Data Size Limits

- **Request Body:** Max 5 MB (typical floor plan: 50-500 KB)
- **Response Body:** Max 10 MB
- **SVG File:** Max 10 MB (typical: 200 KB - 2 MB)

---

## 10. Monitoring & Observability

### 10.1 Metrics to Track

- Request rate per endpoint (requests/sec)
- Error rate by error code (%)
- Response time percentiles (p50, p95, p99)
- Payload size distribution
- Firebase Storage upload success rate

### 10.2 Logging

**Log Level:** INFO for successful requests, ERROR for failures

**Example Log Entry:**
```json
{
  "timestamp": "2026-02-15T16:50:00Z",
  "level": "INFO",
  "method": "PUT",
  "path": "/api/v1/properties/prop_789xyz/floor_plans/fp_abc123",
  "status": 200,
  "latency_ms": 145,
  "user_id": "user_456def",
  "request_id": "req_xyz789"
}
```

---

## References

- [ADR-001: Floor Plan Editor Architecture](./ADR-001-floor-plan-editor-architecture.md)
- [Data Models & Schemas](./floor-plan-data-models.md)
- [Flask RESTful API Best Practices](https://flask-restful.readthedocs.io/)
- [Firebase Storage Documentation](https://firebase.google.com/docs/storage)

---

**Status:** ✅ Ready for Implementation  
**Next Steps:**
1. @BackendLead: Implement Flask routes and OpenAPI spec
2. @MobileLead: Implement API client in Flutter with error handling
3. @QAGuard: Create Postman/Pact test suite for API contract validation
