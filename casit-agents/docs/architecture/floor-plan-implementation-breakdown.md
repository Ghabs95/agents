# Floor Plan Editor - Implementation Breakdown

**Document Version:** 1.0  
**Date:** 2026-02-15  
**Owner:** @Architect  
**Related:** [ADR-001](./ADR-001-floor-plan-editor-architecture.md), [Issue #4](https://github.com/Ghabs95/agents/issues/4)

---

## Overview

This document provides a detailed task breakdown for implementing the Floor Plan Editor feature across the Case Italia stack. Tasks are organized by phase, with estimates, dependencies, and ownership clearly defined.

**Total Effort Estimate:** 13-21 days (2.5-4 weeks)  
**Team:** @MobileLead (Flutter), @BackendLead (API), @CloudArch (Infrastructure)

---

## Phase 1: Foundation (Week 1 - Days 1-7)

**Goal:** Establish base architecture and integration points

### Mobile Tasks (@MobileLead)

#### Task 1.1: Package Integration & Setup
**Estimate:** 1 day  
**Priority:** P0 (Blocker)  
**Dependencies:** None

**Subtasks:**
1. Add `floor_plan_editor: ^2.0.0` to `pubspec.yaml`
2. Add `json_annotation: ^4.8.0` and `equatable: ^2.0.5`
3. Run `flutter pub get` and resolve any conflicts
4. Create feature directory: `lib/features/floor_plan_editor/`
5. Set up folder structure:
   ```
   lib/features/floor_plan_editor/
   ├── bloc/
   ├── models/
   ├── repositories/
   ├── screens/
   ├── widgets/
   └── services/
   ```

**Acceptance Criteria:**
- ✅ Package builds without errors
- ✅ Example from package documentation renders in test screen
- ✅ No dependency conflicts

---

#### Task 1.2: Data Models Implementation
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 1.1

**Subtasks:**
1. Create Dart models (see `floor-plan-data-models.md`):
   - `FloorPlan` entity with JSON serialization
   - `Room` entity with `RoomType` enum
   - `Corner` entity with `CornerType` enum
   - `Door` entity with `DoorType` enum
   - `Window` entity with `WindowType` enum
2. Generate JSON serialization code: `flutter pub run build_runner build`
3. Add `copyWith` methods for immutability
4. Implement `Equatable` for value equality
5. Write unit tests for JSON serialization/deserialization

**Files to Create:**
- `lib/models/floor_plan/floor_plan.dart`
- `lib/models/floor_plan/room.dart`
- `lib/models/floor_plan/corner.dart`
- `lib/models/floor_plan/door.dart`
- `lib/models/floor_plan/window.dart`
- `test/models/floor_plan_test.dart`

**Acceptance Criteria:**
- ✅ All models serialize to/from JSON correctly
- ✅ Unit tests pass with 100% coverage for models
- ✅ `copyWith` methods preserve immutability

---

#### Task 1.3: BLoC State Management Setup
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 1.2

**Subtasks:**
1. Create `FloorPlanBloc` with events and states (see ADR-001)
2. Define events:
   - `LoadFloorPlan`
   - `UpdateCorner`
   - `AddRoom`, `UpdateRoom`, `DeleteRoom`
   - `AddDoor`, `AddWindow`
   - `EnableSnapping`, `DisableSnapping`
   - `RotateRoom`, `MoveRoom`
   - `SaveFloorPlan`
   - `UndoAction`, `RedoAction`
3. Define states:
   - `FloorPlanInitial`
   - `FloorPlanLoading`
   - `FloorPlanLoaded` (with undo/redo stacks)
   - `FloorPlanSaving`
   - `FloorPlanSaved`
   - `FloorPlanError`
4. Implement undo/redo logic with stack management
5. Write BLoC tests (mock repository)

**Files to Create:**
- `lib/features/floor_plan_editor/bloc/floor_plan_bloc.dart`
- `lib/features/floor_plan_editor/bloc/floor_plan_event.dart`
- `lib/features/floor_plan_editor/bloc/floor_plan_state.dart`
- `test/features/floor_plan_editor/bloc/floor_plan_bloc_test.dart`

**Acceptance Criteria:**
- ✅ BLoC handles all defined events
- ✅ Undo/redo works correctly (test with 3+ actions)
- ✅ BLoC tests achieve 85%+ coverage

---

#### Task 1.4: API Repository Layer
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 1.2, Backend Task 2.1

**Subtasks:**
1. Create `FloorPlanRepository` interface
2. Implement `FloorPlanApiRepository` with Dio/http client
3. Implement API methods:
   - `createFloorPlan(propertyId, floorPlan)`
   - `getFloorPlan(propertyId, floorPlanId)`
   - `listFloorPlans(propertyId)`
   - `updateFloorPlan(propertyId, floorPlanId, floorPlan)`
   - `deleteFloorPlan(propertyId, floorPlanId)`
4. Add error handling with typed exceptions:
   - `ApiException`, `ValidationException`, `NetworkException`
5. Implement retry logic for network failures (exponential backoff)
6. Write repository tests with mock HTTP responses

**Files to Create:**
- `lib/features/floor_plan_editor/repositories/floor_plan_repository.dart`
- `lib/features/floor_plan_editor/repositories/floor_plan_api_repository.dart`
- `lib/core/exceptions/api_exceptions.dart`
- `test/features/floor_plan_editor/repositories/floor_plan_repository_test.dart`

**Acceptance Criteria:**
- ✅ All CRUD operations work with backend API (integration test)
- ✅ Error handling covers network failures, 4xx, 5xx errors
- ✅ Retry logic tested with flaky network simulation

---

### Backend Tasks (@BackendLead)

#### Task 2.1: Database Schema & Models
**Estimate:** 1 day  
**Priority:** P0 (Blocker)  
**Dependencies:** None

**Subtasks:**
1. Create PostgreSQL migration: `floor_plans` table (see `floor-plan-data-models.md`)
2. Add indexes: `property_id`, `updated_at`, GIN index on `data` JSONB
3. Create Pydantic models:
   - `FloorPlanModel`
   - `FloorPlanCreateRequest`
   - `FloorPlanUpdateRequest`
   - `FloorPlanResponse`
4. Add server-side validation logic (see `floor-plan-data-models.md` Section 3.2)
5. Write model tests

**Files to Create:**
- `casit_be/migrations/versions/XXXX_add_floor_plans_table.py`
- `casit_be/models/floor_plan.py`
- `casit_be/validators/floor_plan_validators.py`
- `tests/models/test_floor_plan.py`

**Acceptance Criteria:**
- ✅ Migration runs successfully on dev database
- ✅ Pydantic models validate sample payloads without errors
- ✅ Invalid payloads raise validation errors with clear messages

---

#### Task 2.2: Flask API Endpoints
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 2.1

**Subtasks:**
1. Create Flask Blueprint: `floor_plans_bp`
2. Implement routes (see `floor-plan-api-contracts.md`):
   - `POST /api/v1/properties/{id}/floor_plans`
   - `GET /api/v1/properties/{id}/floor_plans/{fp_id}`
   - `GET /api/v1/properties/{id}/floor_plans`
   - `PUT /api/v1/properties/{id}/floor_plans/{fp_id}`
   - `PATCH /api/v1/properties/{id}/floor_plans/{fp_id}`
   - `DELETE /api/v1/properties/{id}/floor_plans/{fp_id}`
3. Add JWT authentication middleware
4. Implement authorization checks (see `floor-plan-api-contracts.md` Section 4.2)
5. Add request validation with error responses
6. Write API integration tests (pytest + Flask test client)

**Files to Create:**
- `casit_be/routes/floor_plans.py`
- `casit_be/middleware/auth.py` (if not exists)
- `tests/routes/test_floor_plans.py`

**Acceptance Criteria:**
- ✅ All endpoints return correct status codes
- ✅ Postman/curl tests pass for happy path + error cases
- ✅ Unauthorized requests return 401
- ✅ Integration tests achieve 90%+ coverage

---

#### Task 2.3: OpenAPI Specification
**Estimate:** 0.5 days  
**Priority:** P1  
**Dependencies:** Task 2.2

**Subtasks:**
1. Generate OpenAPI 3.0 spec from Flask routes (use `flask-swagger-ui`)
2. Document all request/response schemas
3. Add example payloads for each endpoint
4. Host Swagger UI at `/api/v1/docs`

**Files to Create:**
- `casit_be/openapi.yaml` (or auto-generated)
- Update `casit_be/app.py` to serve Swagger UI

**Acceptance Criteria:**
- ✅ Swagger UI renders all endpoints correctly
- ✅ "Try it out" feature works for GET endpoints

---

### Infrastructure Tasks (@CloudArch)

#### Task 3.1: Firebase Storage Setup
**Estimate:** 0.5 days  
**Priority:** P0 (Blocker)  
**Dependencies:** None

**Subtasks:**
1. Create Firebase Storage bucket: `caseitalia-floor-plans`
2. Set up CORS rules for web access (if needed)
3. Configure security rules:
   ```javascript
   rules_version = '2';
   service firebase.storage {
     match /b/{bucket}/o {
       match /floor_plans/{propertyId}/{floorPlanId}.svg {
         allow read: if request.auth != null;
         allow write: if request.auth != null && request.auth.token.roles.hasAny(['surveyor', 'property_editor']);
       }
     }
   }
   ```
4. Test upload/download with Firebase SDK
5. Document Firebase configuration in README

**Acceptance Criteria:**
- ✅ Authenticated users can upload SVG files
- ✅ Unauthenticated requests are denied
- ✅ Download URLs are publicly accessible (signed URLs)

---

## Phase 2: Core Features (Week 2-3 - Days 8-14)

**Goal:** Implement floor plan editing features

### Mobile Tasks (@MobileLead)

#### Task 4.1: Floor Plan Editor Screen UI
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 1.3, Task 1.4

**Subtasks:**
1. Create `FloorPlanEditorScreen` with scaffold
2. Integrate `FloorPlanEditorCanvas` from `floor_plan_editor` package
3. Add top toolbar:
   - Undo/Redo buttons
   - Save button
   - Zoom controls
   - Toggle snapping (on/off)
4. Add bottom drawer for tools:
   - Add Room
   - Add Door
   - Add Window
   - Edit Corner
5. Implement navigation from `PropertyDetailsScreen`:
   - FAB: "Edit Floor Plan" → `FloorPlanEditorScreen`
6. Handle loading state (skeleton loader)
7. Handle error state (error dialog with retry)

**Files to Create:**
- `lib/features/floor_plan_editor/screens/floor_plan_editor_screen.dart`
- `lib/features/floor_plan_editor/widgets/editor_toolbar.dart`
- `lib/features/floor_plan_editor/widgets/tools_drawer.dart`

**Acceptance Criteria:**
- ✅ Screen renders canvas without errors
- ✅ Navigation from property details works
- ✅ Loading/error states display correctly

---

#### Task 4.2: Corner Auto-Generation Logic
**Estimate:** 2 days  
**Priority:** P1  
**Dependencies:** Task 4.1

**Subtasks:**
1. Create `CornerAutoGenerator` service
2. Implement algorithm (see `floor-plan-data-models.md` pseudocode):
   - When door/window is placed, calculate perpendicular offsets
   - Auto-spawn two corners at ~50-100px from opening edges
   - Store corner positions with `isAutoGenerated: true` and `parentOpeningId`
3. Integrate with BLoC: `AddDoor` → trigger `CornerAutoGenerator`
4. Add visual indicators for auto-generated corners (different color/icon)
5. Write unit tests for corner calculation logic

**Files to Create:**
- `lib/features/floor_plan_editor/services/corner_auto_generator.dart`
- `test/features/floor_plan_editor/services/corner_auto_generator_test.dart`

**Acceptance Criteria:**
- ✅ Placing door creates 2 corners automatically
- ✅ Corner positions are correct (perpendicular to opening)
- ✅ Deleting door removes associated auto-generated corners
- ✅ Unit tests cover edge cases (corners at canvas boundary)

---

#### Task 4.3: Snap-to-Corner Drag & Drop
**Estimate:** 3 days  
**Priority:** P1  
**Dependencies:** Task 4.2

**Subtasks:**
1. Create `SnappingController` service
2. Implement proximity detection:
   - Calculate Euclidean distance between drag position and all corners
   - If distance < `snapRadius` (30px), return nearest corner
3. Add visual feedback during drag:
   - Highlight target corner with animation (pulse effect)
   - Show snap indicator line
4. On drop:
   - Attach element to nearest corner (update position)
   - Emit `UpdateCorner` event to BLoC
5. Add "Toggle Snapping" button in toolbar (enable/disable feature)
6. Write integration tests (widget test with drag simulation)

**Files to Create:**
- `lib/features/floor_plan_editor/services/snapping_controller.dart`
- `lib/features/floor_plan_editor/widgets/snap_indicator.dart`
- `test/features/floor_plan_editor/services/snapping_controller_test.dart`
- `test/features/floor_plan_editor/widgets/floor_plan_editor_screen_test.dart`

**Acceptance Criteria:**
- ✅ Dragging door near corner shows visual feedback
- ✅ Dropping door within snap radius attaches to corner
- ✅ Dropping outside snap radius places door freely
- ✅ Toggle button enables/disables snapping
- ✅ Widget tests simulate drag-and-drop successfully

---

#### Task 4.4: Room Rotation & Movement
**Estimate:** 2 days  
**Priority:** P2  
**Dependencies:** Task 4.1

**Subtasks:**
1. Create `RoomTransformHandler` service
2. Add rotation handles:
   - Circular anchors at room corners
   - Drag handle to rotate room around center point
3. Implement rotation logic:
   - Use `Transform.rotate()` or `Matrix4` for room widget
   - Update `room.rotation` value in degrees (0-360)
   - Rotate all child elements (doors/windows) accordingly
4. Add movement controls:
   - Pan gesture to move entire room
   - Update all vertex positions
5. Add multi-touch support (pinch-to-zoom, two-finger rotate)
6. Write widget tests for rotation and movement

**Files to Create:**
- `lib/features/floor_plan_editor/services/room_transform_handler.dart`
- `lib/features/floor_plan_editor/widgets/rotation_handle.dart`
- `test/features/floor_plan_editor/services/room_transform_handler_test.dart`

**Acceptance Criteria:**
- ✅ Dragging rotation handle rotates room smoothly
- ✅ Pan gesture moves room without distortion
- ✅ Child elements (doors/windows) move/rotate with room
- ✅ Multi-touch gestures work on physical device

---

#### Task 4.5: Corner Value Editing UI
**Estimate:** 2 days  
**Priority:** P2  
**Dependencies:** Task 4.2

**Subtasks:**
1. Create `CornerPropertiesPanel` widget (bottom sheet)
2. Add input fields:
   - X coordinate (numeric, read-only or editable)
   - Y coordinate (numeric, read-only or editable)
   - Value (measurement, e.g., "30 cm from wall")
   - Unit selector (dropdown: cm, m, in, ft)
3. Add validation:
   - X/Y must be within canvas bounds
   - Value must be positive number
4. Implement two-way binding:
   - Input changes → emit `UpdateCorner` event
   - BLoC state changes → update input fields
5. Add "Delete Corner" button (only for non-auto-generated corners)
6. Write widget tests for input validation

**Files to Create:**
- `lib/features/floor_plan_editor/widgets/corner_properties_panel.dart`
- `test/features/floor_plan_editor/widgets/corner_properties_panel_test.dart`

**Acceptance Criteria:**
- ✅ Tapping corner opens properties panel
- ✅ Editing value updates corner in canvas
- ✅ Validation prevents invalid input (e.g., negative values)
- ✅ Deleting corner removes it from floor plan

---

#### Task 4.6: Save Floor Plan & SVG Export
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 4.1, Task 3.1

**Subtasks:**
1. Implement "Save" button handler in `FloorPlanEditorScreen`
2. Export floor plan as SVG using `floor_plan_editor` package API
3. Upload SVG to Firebase Storage:
   - Path: `/floor_plans/{propertyId}/{floorPlanId}.svg`
   - Get download URL
4. Send floor plan JSON + SVG URL to backend API:
   - `POST` if new, `PUT` if editing existing
5. Show success/error toast notification
6. Navigate back to `PropertyDetailsScreen` on success
7. Handle offline mode:
   - Save locally (SQLite or Hive)
   - Sync when online (background job)

**Files to Create:**
- `lib/features/floor_plan_editor/services/floor_plan_export_service.dart`
- `lib/features/floor_plan_editor/services/firebase_storage_service.dart`
- `test/features/floor_plan_editor/services/floor_plan_export_service_test.dart`

**Acceptance Criteria:**
- ✅ Saving floor plan uploads SVG and sends JSON to API
- ✅ Success toast shows: "Floor plan saved successfully"
- ✅ Error toast shows if upload/API call fails
- ✅ Offline saves queue for later sync

---

## Phase 3: Polish & Testing (Week 3-4 - Days 15-21)

**Goal:** Refinement, testing, and optimization

### Mobile Tasks (@MobileLead)

#### Task 5.1: Performance Optimization
**Estimate:** 2 days  
**Priority:** P1  
**Dependencies:** All Phase 2 tasks

**Subtasks:**
1. Profile canvas rendering on mid/low-end devices:
   - Use Flutter DevTools → Performance tab
   - Target: 60fps for floor plans with <50 elements
2. Optimize rendering:
   - Implement `RepaintBoundary` for static elements
   - Use `CustomPaint` caching for repeated shapes
   - Reduce overdraw (minimize overlapping layers)
3. Optimize state updates:
   - Debounce rapid corner position changes (drag)
   - Batch BLoC events during multi-element operations
4. Add frame rate monitoring overlay (debug mode)
5. Test on low-end device (e.g., Android with 2GB RAM)

**Acceptance Criteria:**
- ✅ 60fps maintained during drag operations
- ✅ No jank when adding/removing elements
- ✅ App memory usage < 150MB during editing

---

#### Task 5.2: Animations & Visual Feedback
**Estimate:** 1 day  
**Priority:** P2  
**Dependencies:** Task 5.1

**Subtasks:**
1. Add micro-interactions:
   - Smooth fade-in for new elements
   - Bounce animation when snapping to corner
   - Ripple effect on tool selection
2. Add loading skeleton for canvas
3. Add success checkmark animation on save
4. Ensure animations respect system accessibility settings (reduce motion)

**Files to Create:**
- `lib/features/floor_plan_editor/animations/snap_animation.dart`
- `lib/features/floor_plan_editor/animations/success_animation.dart`

**Acceptance Criteria:**
- ✅ Animations feel smooth and natural
- ✅ Accessibility settings disable animations
- ✅ No performance impact from animations

---

### QA Tasks (@QAGuard)

#### Task 6.1: Unit Test Coverage
**Estimate:** 2 days  
**Priority:** P0 (Blocker)  
**Dependencies:** All Phase 2 tasks

**Subtasks:**
1. Run coverage report: `flutter test --coverage`
2. Ensure 70%+ coverage for:
   - Models (target: 100%)
   - BLoC (target: 85%)
   - Services (target: 80%)
   - Widgets (target: 60%)
3. Write missing tests for edge cases:
   - Empty floor plan
   - Floor plan with 100+ elements (stress test)
   - Concurrent undo/redo operations
4. Add golden tests for key widgets (visual regression)

**Acceptance Criteria:**
- ✅ Overall coverage ≥ 70%
- ✅ All critical paths have tests
- ✅ No flaky tests (run 10 times, 100% pass rate)

---

#### Task 6.2: Integration & E2E Tests
**Estimate:** 2 days  
**Priority:** P1  
**Dependencies:** Task 6.1, Backend Task 2.2

**Subtasks:**
1. Write integration tests (Flutter integration_test):
   - Create floor plan → add room → add door → save
   - Edit existing floor plan → rotate room → save
   - Delete floor plan
2. Test offline mode:
   - Save floor plan offline → go online → verify sync
3. Test error handling:
   - Backend returns 500 → show error dialog
   - Network timeout → show retry prompt
4. Run tests on multiple devices:
   - Android (API 29+)
   - iOS (14+)

**Files to Create:**
- `integration_test/floor_plan_editor_test.dart`
- `integration_test/floor_plan_offline_test.dart`

**Acceptance Criteria:**
- ✅ All integration tests pass on Android & iOS
- ✅ Offline mode tested with airplane mode
- ✅ Error scenarios tested with mock server

---

#### Task 6.3: API Contract Tests (Backend)
**Estimate:** 1 day  
**Priority:** P1  
**Dependencies:** Backend Task 2.2

**Subtasks:**
1. Set up Pact contract tests (Flutter → Python)
2. Verify API responses match contract:
   - `POST /floor_plans` returns 201 with expected schema
   - `GET /floor_plans/:id` returns 200 with full floor plan data
   - `PUT /floor_plans/:id` returns 200 with updated data
   - Error responses match error schema
3. Run Pact broker for contract sharing
4. Automate contract tests in CI/CD

**Files to Create:**
- `test/pact/floor_plan_api_contract_test.dart` (Flutter)
- `tests/contracts/floor_plan_api_pact_test.py` (Backend)

**Acceptance Criteria:**
- ✅ All contract tests pass
- ✅ Breaking changes detected automatically
- ✅ Pact broker dashboard shows green status

---

### Backend Tasks (@BackendLead)

#### Task 7.1: API Performance Testing
**Estimate:** 1 day  
**Priority:** P1  
**Dependencies:** Backend Task 2.2

**Subtasks:**
1. Set up load testing with Locust or k6
2. Test API under load:
   - 100 concurrent users creating floor plans
   - 500 requests/sec for GET endpoints
3. Verify SLA targets (see `floor-plan-api-contracts.md` Section 9.1):
   - `GET /floor_plans/:id` p95 < 100ms
   - `POST /floor_plans` p95 < 200ms
4. Identify bottlenecks with profiling (cProfile)
5. Optimize slow queries (add indexes if needed)

**Acceptance Criteria:**
- ✅ API meets latency SLAs under load
- ✅ No database connection pool exhaustion
- ✅ Error rate < 0.1% under normal load

---

#### Task 7.2: Monitoring & Logging
**Estimate:** 0.5 days  
**Priority:** P1  
**Dependencies:** Backend Task 2.2

**Subtasks:**
1. Add structured logging for all endpoints (see `floor-plan-api-contracts.md` Section 10.2)
2. Set up metrics dashboard (Prometheus + Grafana):
   - Request rate per endpoint
   - Error rate by status code
   - p50/p95/p99 latency
3. Configure alerts:
   - Error rate > 1% for 5 minutes
   - p95 latency > 500ms for 5 minutes

**Acceptance Criteria:**
- ✅ All requests logged with request_id
- ✅ Grafana dashboard shows real-time metrics
- ✅ Alerts trigger on test failures

---

## Phase 4: Documentation & Deployment (Days 20-21)

### Documentation Tasks (@Scribe)

#### Task 8.1: User Documentation
**Estimate:** 1 day  
**Priority:** P2  
**Dependencies:** All features complete

**Subtasks:**
1. Write user guide: "How to Create a Floor Plan"
2. Add screenshots/GIFs for each step
3. Document tool usage (corner snapping, rotation, etc.)
4. Add FAQs:
   - "How do I delete a corner?"
   - "Why can't I rotate this room?"
5. Translate to Italian (if needed)

**Files to Create:**
- `docs/user-guides/floor-plan-editor.md`

---

#### Task 8.2: Developer Documentation
**Estimate:** 0.5 days  
**Priority:** P2  
**Dependencies:** All features complete

**Subtasks:**
1. Update README with floor plan editor section
2. Document API endpoints (link to OpenAPI spec)
3. Add architecture diagrams (Mermaid or draw.io)
4. Document environment variables for Firebase config

**Files to Update:**
- `casit-app/README.md`
- `casit-be/README.md`
- Link from main repo README

---

### Deployment Tasks (@OpsCommander)

#### Task 9.1: Staging Deployment
**Estimate:** 0.5 days  
**Priority:** P0 (Blocker)  
**Dependencies:** All features complete, Task 6.2

**Subtasks:**
1. Deploy backend to staging environment
2. Deploy mobile app to TestFlight (iOS) / Firebase App Distribution (Android)
3. Run smoke tests on staging:
   - Create floor plan
   - Edit floor plan
   - Delete floor plan
4. Verify Firebase Storage permissions
5. Check monitoring dashboard for errors

**Acceptance Criteria:**
- ✅ Staging environment accessible to team
- ✅ All smoke tests pass
- ✅ No errors in logs

---

#### Task 9.2: Production Deployment
**Estimate:** 0.5 days  
**Priority:** P0 (Blocker)  
**Dependencies:** Task 9.1, @Ghabs approval

**Subtasks:**
1. Run database migration on production (with rollback plan)
2. Deploy backend API (blue-green deployment)
3. Submit mobile app to App Store / Google Play
4. Monitor production for 24 hours:
   - Check error rate (target: < 0.1%)
   - Check latency (target: p95 < 200ms)
   - Verify Firebase Storage uploads work
5. Prepare rollback plan (revert migration + API version)

**Acceptance Criteria:**
- ✅ Zero-downtime deployment
- ✅ No spike in error rate after deployment
- ✅ Mobile app approved by app stores

---

## Risk Management

### High-Priority Risks

| Risk | Mitigation | Owner |
|------|------------|-------|
| `floor_plan_editor` package has breaking bug | Test package thoroughly in Task 1.1; have custom canvas as backup | @MobileLead |
| Backend API performance issues | Load testing in Task 7.1; add database indexes early | @BackendLead |
| Complex snapping logic bugs | Unit tests + iterative refinement in Task 4.3 | @MobileLead |
| Firebase Storage quota exceeded | Monitor usage; implement CDN caching; compress SVGs | @CloudArch |
| Concurrent editing conflicts | Implement optimistic locking with version field | @BackendLead |

---

## Dependencies Graph

```
Phase 1 (Foundation)
├─ Mobile: 1.1 → 1.2 → 1.3 → 1.4
├─ Backend: 2.1 → 2.2 → 2.3
└─ Infra: 3.1 (parallel)

Phase 2 (Core Features)
├─ Mobile: 4.1 → 4.2 → 4.3, 4.4, 4.5 → 4.6
└─ (All depend on Phase 1)

Phase 3 (Polish & Testing)
├─ Mobile: 5.1 → 5.2
├─ QA: 6.1 → 6.2, 6.3
└─ Backend: 7.1, 7.2

Phase 4 (Docs & Deploy)
├─ Docs: 8.1, 8.2
└─ Deploy: 9.1 → 9.2
```

---

## Team Assignments

| Owner | Tasks | Total Effort |
|-------|-------|--------------|
| **@MobileLead** | 1.1, 1.2, 1.3, 1.4, 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 5.1, 5.2 | 22 days |
| **@BackendLead** | 2.1, 2.2, 2.3, 7.1, 7.2 | 6 days |
| **@CloudArch** | 3.1 | 0.5 days |
| **@QAGuard** | 6.1, 6.2, 6.3 | 5 days |
| **@Scribe** | 8.1, 8.2 | 1.5 days |
| **@OpsCommander** | 9.1, 9.2 | 1 day |

**Note:** Mobile tasks can be parallelized with 2 developers (reduce 22 days → 13-15 days wall time).

---

## Success Criteria

**Definition of Done:**
- ✅ All tasks complete with acceptance criteria met
- ✅ 70%+ test coverage (mobile), 90%+ test coverage (backend)
- ✅ Integration tests pass on Android & iOS
- ✅ API contract tests pass
- ✅ Staging deployment successful with smoke tests
- ✅ Production deployment with zero-downtime
- ✅ @QAGuard sign-off
- ✅ @Privacy sign-off (if user data involved)
- ✅ Documentation complete (user + developer)

**Release Readiness:**
- ✅ Performance meets SLAs (see `floor-plan-api-contracts.md`)
- ✅ Error rate < 0.1% in staging for 48 hours
- ✅ No Critical/High bugs outstanding
- ✅ Rollback plan documented and tested

---

## References

- [ADR-001: Floor Plan Editor Architecture](./ADR-001-floor-plan-editor-architecture.md)
- [Data Models & Schemas](./floor-plan-data-models.md)
- [API Contracts](./floor-plan-api-contracts.md)
- [Atlas Technical Assessment](https://github.com/Ghabs95/agents/issues/4#issuecomment-3904524433)
- [Case Italia Workflows](../../WORKFLOWS.md)

---

**Status:** ✅ Ready for Team Review  
**Next Actions:**
1. @MobileLead: Review task breakdown, adjust estimates if needed
2. @BackendLead: Confirm API implementation timeline
3. @ProjectLead: Create GitHub issues from tasks and assign to sprint
4. @Atlas: Approve resource allocation and timeline

**Prepared by:** @Architect  
**Approval Pending:** @Atlas, @MobileLead, @BackendLead
