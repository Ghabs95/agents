# Floor Plan Editor - Component Architecture

**Document Version:** 1.0  
**Date:** 2026-02-15  
**Owner:** @Architect  
**Related:** [ADR-001](./ADR-001-floor-plan-editor-architecture.md)

---

## 1. System Context Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     Case Italia Ecosystem                       │
│                                                                 │
│  ┌──────────────┐       ┌──────────────┐       ┌────────────┐ │
│  │  casit-app   │◄─────►│   casit-be   │◄─────►│ PostgreSQL │ │
│  │  (Flutter)   │       │   (Flask)    │       │  Database  │ │
│  └──────┬───────┘       └──────────────┘       └────────────┘ │
│         │                                                       │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────┐                                              │
│  │   Firebase   │                                              │
│  │   Storage    │                                              │
│  │  (SVG/PNG)   │                                              │
│  └──────────────┘                                              │
└─────────────────────────────────────────────────────────────────┘

External Dependencies:
- floor_plan_editor package (pub.dev) → Canvas rendering
- Firebase SDK → Storage & Auth
```

---

## 2. Mobile App (casit-app) Architecture

### 2.1 Feature Module Structure

```
lib/
├── features/
│   └── floor_plan_editor/
│       ├── bloc/                    # State management
│       │   ├── floor_plan_bloc.dart
│       │   ├── floor_plan_event.dart
│       │   └── floor_plan_state.dart
│       ├── models/                  # Data entities
│       │   ├── floor_plan.dart
│       │   ├── room.dart
│       │   ├── corner.dart
│       │   ├── door.dart
│       │   └── window.dart
│       ├── repositories/            # Data layer
│       │   ├── floor_plan_repository.dart (interface)
│       │   └── floor_plan_api_repository.dart (impl)
│       ├── screens/                 # UI pages
│       │   └── floor_plan_editor_screen.dart
│       ├── widgets/                 # Reusable components
│       │   ├── editor_toolbar.dart
│       │   ├── tools_drawer.dart
│       │   ├── corner_properties_panel.dart
│       │   ├── snap_indicator.dart
│       │   └── rotation_handle.dart
│       └── services/                # Business logic
│           ├── corner_auto_generator.dart
│           ├── snapping_controller.dart
│           ├── room_transform_handler.dart
│           ├── floor_plan_export_service.dart
│           └── firebase_storage_service.dart
└── core/
    ├── exceptions/
    │   └── api_exceptions.dart
    └── validators/
        └── floor_plan_validators.dart
```

### 2.2 Component Relationships

```
┌─────────────────────────────────────────────────────────────┐
│            FloorPlanEditorScreen (UI)                       │
│  ┌────────────────────────────────────────────────────────┐ │
│  │  BlocBuilder<FloorPlanBloc, FloorPlanState>           │ │
│  │  ├─ EditorToolbar (undo/redo/save/zoom)               │ │
│  │  ├─ FloorPlanEditorCanvas (floor_plan_editor pkg)     │ │
│  │  │   └─ Renders: Rooms, Corners, Doors, Windows       │ │
│  │  ├─ ToolsDrawer (add room/door/window/corner)         │ │
│  │  └─ CornerPropertiesPanel (edit corner values)        │ │
│  └────────────────────────────────────────────────────────┘ │
│                           │                                  │
│                           ▼ (events)                         │
│  ┌────────────────────────────────────────────────────────┐ │
│  │              FloorPlanBloc                             │ │
│  │  - Manages FloorPlanState (loaded/loading/error)      │ │
│  │  - Undo/Redo stack management                         │ │
│  │  - Delegates to services for business logic           │ │
│  └────────────┬────────────────────────┬──────────────────┘ │
│               │                        │                     │
│               ▼ (data operations)      ▼ (feature logic)    │
│  ┌────────────────────────┐  ┌────────────────────────────┐ │
│  │  FloorPlanRepository   │  │  Services:                 │ │
│  │  - createFloorPlan()   │  │  - CornerAutoGenerator     │ │
│  │  - getFloorPlan()      │  │  - SnappingController      │ │
│  │  - updateFloorPlan()   │  │  - RoomTransformHandler    │ │
│  │  - deleteFloorPlan()   │  │  - FloorPlanExportService  │ │
│  └────────────┬───────────┘  └────────────────────────────┘ │
│               │                                               │
│               ▼ (HTTP calls)                                 │
│  ┌────────────────────────┐                                  │
│  │   Backend API Client   │                                  │
│  │   (Dio / http)         │                                  │
│  └────────────────────────┘                                  │
└─────────────────────────────────────────────────────────────┘
```

### 2.3 BLoC Event Flow

```
User Action (UI)
      ┃
      ▼
Add Door Event ───────────────┐
      ┃                       │
      ▼                       │
FloorPlanBloc                 │
      ┃                       │
      ├─ 1. Add door to state │
      │                       │
      ├─ 2. Call CornerAutoGenerator ◄───┘
      │    └─ Generate 2 corners near door
      │
      ├─ 3. Update state with new corners
      │
      └─ 4. Emit FloorPlanLoaded(updated)
            ┃
            ▼
      BlocBuilder rebuilds UI
            ┃
            ▼
      Canvas re-renders with door + corners
```

---

## 3. Backend (casit-be) Architecture

### 3.1 Backend Module Structure

```
casit_be/
├── routes/
│   └── floor_plans.py          # Flask Blueprint for API endpoints
├── models/
│   └── floor_plan.py           # Pydantic models
├── validators/
│   └── floor_plan_validators.py # Server-side validation
├── services/
│   └── floor_plan_service.py   # Business logic (future)
├── repositories/
│   └── floor_plan_repository.py # Database access layer (future)
└── middleware/
    ├── auth.py                  # JWT authentication
    └── rate_limiter.py          # Rate limiting
```

### 3.2 Request Flow

```
Mobile App
    ┃
    ▼ HTTP Request
┌───────────────────────────────────────┐
│  Flask API (casit-be)                 │
│                                       │
│  ┌─────────────────────────────────┐ │
│  │  Middleware Layer               │ │
│  │  1. CORS Handler                │ │
│  │  2. JWT Authentication          │ │
│  │  3. Rate Limiter                │ │
│  └────────┬────────────────────────┘ │
│           │                           │
│           ▼                           │
│  ┌─────────────────────────────────┐ │
│  │  Floor Plans Blueprint          │ │
│  │  /api/v1/properties/{id}/...    │ │
│  │                                 │ │
│  │  POST   /floor_plans            │ │
│  │  GET    /floor_plans/:id        │ │
│  │  PUT    /floor_plans/:id        │ │
│  │  DELETE /floor_plans/:id        │ │
│  └────────┬────────────────────────┘ │
│           │                           │
│           ▼                           │
│  ┌─────────────────────────────────┐ │
│  │  Request Validation             │ │
│  │  (Pydantic Models)              │ │
│  │  - FloorPlanCreateRequest       │ │
│  │  - FloorPlanUpdateRequest       │ │
│  └────────┬────────────────────────┘ │
│           │                           │
│           ▼                           │
│  ┌─────────────────────────────────┐ │
│  │  Database Operations            │ │
│  │  (SQLAlchemy ORM)               │ │
│  │  - INSERT INTO floor_plans      │ │
│  │  - SELECT FROM floor_plans      │ │
│  │  - UPDATE floor_plans           │ │
│  └────────┬────────────────────────┘ │
│           │                           │
└───────────┼───────────────────────────┘
            │
            ▼
    ┌──────────────┐
    │  PostgreSQL  │
    │   Database   │
    └──────────────┘
```

### 3.3 Database Schema

```sql
┌──────────────────────────────────────────────────┐
│              floor_plans (table)                 │
├──────────────────────────────────────────────────┤
│ id (VARCHAR, PK)                                 │
│ property_id (VARCHAR, FK → properties.id)        │
│ name (VARCHAR)                                   │
│ data (JSONB) ← stores rooms/corners/doors/windows│
│ metadata (JSONB)                                 │
│ svg_url (TEXT) ← Firebase Storage URL            │
│ created_at (TIMESTAMP)                           │
│ updated_at (TIMESTAMP)                           │
└──────────────────────────────────────────────────┘

Indexes:
- idx_floor_plans_property (property_id)
- idx_floor_plans_updated (updated_at DESC)
- idx_floor_plans_data (data) [GIN index for JSONB queries]
```

---

## 4. Data Flow Architecture

### 4.1 Create Floor Plan Flow

```
User (Mobile App)
      ┃
      ▼ Tap "Edit Floor Plan"
┌─────────────────────────────┐
│  FloorPlanEditorScreen      │
│  - Initialize empty canvas  │
│  - Load property details    │
└─────────────┬───────────────┘
              │
              ▼ Add rooms, doors, windows
┌─────────────────────────────┐
│  User Edits Floor Plan      │
│  - Draw rooms               │
│  - Place doors/windows      │
│  - Auto-generated corners   │
│  - Snap elements to corners │
└─────────────┬───────────────┘
              │
              ▼ Tap "Save"
┌─────────────────────────────┐
│  FloorPlanBloc              │
│  - Emit SaveFloorPlan event │
└─────────────┬───────────────┘
              │
              ├──── 1. Export SVG
              │     ┃
              │     ▼
              │  ┌──────────────────────┐
              │  │ FloorPlanExportSvc   │
              │  │ - Generate SVG       │
              │  └──────┬───────────────┘
              │         │
              │         ▼
              │  ┌──────────────────────┐
              │  │ Firebase Storage     │
              │  │ - Upload SVG         │
              │  │ - Return URL         │
              │  └──────┬───────────────┘
              │         │
              │         ▼ svg_url
              ├──── 2. Send JSON + SVG URL
              │     ┃
              │     ▼
              │  ┌──────────────────────┐
              │  │ FloorPlanRepository  │
              │  │ - POST /floor_plans  │
              │  └──────┬───────────────┘
              │         │
              │         ▼
              │  ┌──────────────────────┐
              │  │ Backend API          │
              │  │ - Validate data      │
              │  │ - Insert into DB     │
              │  │ - Return floor_plan  │
              │  └──────┬───────────────┘
              │         │
              │         ▼ 201 Created
              ├──── 3. Update local state
              │     ┃
              │     ▼
              │  ┌──────────────────────┐
              │  │ FloorPlanBloc        │
              │  │ - Emit Saved state   │
              │  └──────┬───────────────┘
              │         │
              ▼         ▼
┌─────────────────────────────┐
│  Success Toast              │
│  "Floor plan saved"         │
│  → Navigate back            │
└─────────────────────────────┘
```

### 4.2 Load & Edit Floor Plan Flow

```
User (Mobile App)
      ┃
      ▼ Open property with existing floor plan
┌─────────────────────────────┐
│  PropertyDetailsScreen      │
│  - Show "Edit Floor Plan"   │
└─────────────┬───────────────┘
              │
              ▼ Tap button
┌─────────────────────────────┐
│  FloorPlanBloc              │
│  - Emit LoadFloorPlan event │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│  FloorPlanRepository        │
│  - GET /floor_plans/:id     │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│  Backend API                │
│  - Query database           │
│  - Return floor plan JSON   │
└─────────────┬───────────────┘
              │
              ▼ 200 OK
┌─────────────────────────────┐
│  FloorPlanBloc              │
│  - Deserialize JSON         │
│  - Emit FloorPlanLoaded     │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│  FloorPlanEditorScreen      │
│  - Render canvas            │
│  - Display rooms/doors/...  │
│  - Enable editing           │
└─────────────────────────────┘
      ┃
      ▼ User edits
┌─────────────────────────────┐
│  Follow "Save" flow above   │
│  (PUT instead of POST)      │
└─────────────────────────────┘
```

---

## 5. Custom Feature Components

### 5.1 CornerAutoGenerator

**Responsibility:** Generate corners near doors/windows

```
Input:
  - Opening (door/window) position
  - Opening width
  - Parent room vertices

Algorithm:
  1. Find nearest wall edge to opening
  2. Calculate perpendicular vector from opening
  3. Spawn 2 corners at ±50px from opening edges
  4. Mark corners with:
     - isAutoGenerated: true
     - parentOpeningId: door/window ID

Output:
  - List<Corner> (2 new corners)

Integration:
  - Called by FloorPlanBloc on AddDoor/AddWindow event
  - Corners added to state automatically
```

### 5.2 SnappingController

**Responsibility:** Detect and snap elements to nearby corners

```
Input:
  - Current drag position (Offset)
  - List of all corners in floor plan
  - Snap radius (default: 30px)

Algorithm:
  1. Calculate Euclidean distance to all corners
  2. Find nearest corner
  3. If distance < snapRadius:
     - Return nearest corner position
     - Trigger visual feedback (highlight corner)
  4. Else:
     - Return original drag position

Output:
  - Snapped position (Offset)
  - Nearest corner (Corner?) for visual feedback

Integration:
  - Called on drag update gesture
  - Updates element position in real-time
```

### 5.3 RoomTransformHandler

**Responsibility:** Handle room rotation and movement

```
Rotation:
  Input: Rotation handle drag offset
  Algorithm:
    1. Calculate angle between drag position and room center
    2. Convert to degrees (0-360)
    3. Apply rotation to room Transform matrix
    4. Rotate all child elements (doors/windows) accordingly
  Output: Updated room.rotation value

Movement:
  Input: Pan gesture delta (dx, dy)
  Algorithm:
    1. Update all room vertex positions by delta
    2. Update child element positions by delta
  Output: Updated room.vertices list

Integration:
  - Rotation handle widget calls rotation logic
  - Pan gesture detector calls movement logic
  - Both update FloorPlanBloc state
```

---

## 6. External Package Integration

### 6.1 floor_plan_editor Package

**Role:** Provides canvas rendering and gesture handling

**Key Components Used:**
- `FloorPlanEditor` widget (main canvas)
- `Room`, `Door`, `Window` shape renderers
- Zoom/pan gesture controllers
- SVG export functionality

**Customization Points:**
- Corner style (color, size, shape)
- Snapping behavior (custom SnappingController)
- Toolbar actions (custom EditorToolbar)

**Abstraction Strategy:**
```dart
// Wrapper service to decouple from package
class FloorPlanEditorService {
  final FloorPlanEditor _editor;

  void addRoom(Room room) {
    // Map our Room model to package's Room model
    _editor.addShape(room.toPackageModel());
  }

  String exportSvg() {
    return _editor.exportToSvg();
  }
}
```

### 6.2 Firebase Storage SDK

**Role:** Upload/download SVG files

**Integration:**
```dart
class FirebaseStorageService {
  final FirebaseStorage _storage;

  Future<String> uploadSvg(String propertyId, String floorPlanId, String svg) async {
    final ref = _storage.ref('floor_plans/$propertyId/$floorPlanId.svg');
    await ref.putString(svg, metadata: SettableMetadata(contentType: 'image/svg+xml'));
    return await ref.getDownloadURL();
  }

  Future<String> downloadSvg(String url) async {
    // Download and return SVG content
  }
}
```

---

## 7. Testing Architecture

### 7.1 Test Pyramid

```
                  ┌───────────────┐
                  │      E2E      │ ← 5-10 tests
                  │ (integration  │   (full flow)
                  │    _test/)    │
                  └───────┬───────┘
                          │
              ┌───────────┴───────────┐
              │   Widget Tests        │ ← 30-50 tests
              │  (screen interactions)│   (UI components)
              └───────────┬───────────┘
                          │
          ┌───────────────┴───────────────┐
          │       Unit Tests              │ ← 100+ tests
          │  (models, BLoC, services)     │   (business logic)
          └───────────────────────────────┘
```

### 7.2 Test Coverage Targets

| Layer | Coverage Target | Focus |
|-------|-----------------|-------|
| Models | 100% | JSON serialization, equality |
| BLoC | 85% | Event handling, state transitions |
| Services | 80% | Corner generation, snapping logic |
| Widgets | 60% | User interactions, rendering |
| Integration | Full flows | Create/edit/save/delete |

---

## 8. Performance Optimization Strategy

### 8.1 Mobile App Optimizations

1. **Rendering:**
   - Use `RepaintBoundary` for static elements (rooms)
   - Cache `CustomPaint` output for repeated shapes
   - Implement viewport culling (only render visible elements)

2. **State Management:**
   - Debounce rapid corner position updates (300ms)
   - Batch multiple BLoC events into single state update
   - Use `Equatable` to prevent unnecessary rebuilds

3. **Data Transfer:**
   - Compress SVG before upload (gzip)
   - Use pagination for large floor plan lists
   - Implement incremental loading (load rooms first, then details)

### 8.2 Backend API Optimizations

1. **Database:**
   - Add GIN index on JSONB columns for fast queries
   - Use database connection pooling (max 20 connections)
   - Cache frequent queries (Redis)

2. **API Response:**
   - Implement ETag caching for GET requests
   - Use HTTP/2 for parallel requests
   - Compress responses (gzip)

---

## 9. Security Architecture

### 9.1 Authentication Flow

```
Mobile App
    ┃
    ▼ Login
┌────────────────┐
│  Firebase Auth │
│  - Email/PW    │
│  - Returns JWT │
└────────┬───────┘
         │
         ▼ JWT token
┌────────────────┐
│  API Request   │
│  Header:       │
│  Authorization:│
│  Bearer {jwt}  │
└────────┬───────┘
         │
         ▼
┌────────────────┐
│  Backend       │
│  - Verify JWT  │
│  - Extract     │
│    user_id     │
│  - Check perms │
└────────────────┘
```

### 9.2 Authorization Rules

| Resource | Action | Required Role |
|----------|--------|---------------|
| Floor Plan | Read | `property_viewer` or owner |
| Floor Plan | Create/Edit | `property_editor` or `surveyor` |
| Floor Plan | Delete | `property_owner` or `admin` |

### 9.3 Data Security

- **In Transit:** HTTPS (TLS 1.3)
- **At Rest:** PostgreSQL encryption, Firebase Storage encryption
- **PII Handling:** Floor plans are property metadata (not PII)
- **GDPR Compliance:** Floor plans deleted when property is deleted (cascade)

---

## 10. Monitoring & Observability

### 10.1 Metrics to Track

**Mobile App:**
- Screen load time (FloorPlanEditorScreen)
- Save operation success rate
- SVG upload success rate
- Crash rate by feature

**Backend API:**
- Request rate per endpoint (req/sec)
- Error rate by status code (%)
- p50/p95/p99 latency
- Database query time

### 10.2 Logging

**Mobile App:**
```dart
logger.info('Floor plan saved', extra: {
  'floor_plan_id': floorPlan.id,
  'property_id': floorPlan.propertyId,
  'num_rooms': floorPlan.rooms.length,
  'num_corners': floorPlan.corners.length,
});
```

**Backend API:**
```python
logger.info('Floor plan created', extra={
    'floor_plan_id': floor_plan.id,
    'property_id': floor_plan.property_id,
    'user_id': current_user.id,
    'request_id': request.id,
    'latency_ms': response_time,
})
```

---

## References

- [ADR-001: Floor Plan Editor Architecture](./ADR-001-floor-plan-editor-architecture.md)
- [Data Models & Schemas](./floor-plan-data-models.md)
- [API Contracts](./floor-plan-api-contracts.md)
- [Implementation Breakdown](./floor-plan-implementation-breakdown.md)
- [Flutter Architecture Patterns](https://docs.flutter.dev/development/data-and-backend/state-mgmt/intro)
- [BLoC Pattern Documentation](https://bloclibrary.dev/)

---

**Status:** ✅ Complete  
**Last Updated:** 2026-02-15  
**Maintained by:** @Architect
