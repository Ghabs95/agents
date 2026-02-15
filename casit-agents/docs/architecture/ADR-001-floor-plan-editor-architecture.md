# ADR-001: Floor Plan Editor Architecture

**Status:** Accepted  
**Date:** 2024-02-15  
**Deciders:** @Architect, @Atlas, @Ghabs  
**Related Issues:** [#4](https://github.com/Ghabs95/agents/issues/4)

---

## Context and Problem Statement

The Case Italia mobile application requires a surveyor floor plan editor to enable property assessors to create, edit, and manage floor plans directly within the app. The feature must support:

1. **Corner Management:** Auto-generation of corners near doors/windows and editable corner values
2. **Snapping:** Intelligent joining of touching corners and drag-to-attach for openings
3. **Room Controls:** Intuitive rotation and movement of room elements

Based on [@Atlas's technical assessment](https://github.com/Ghabs95/agents/issues/4#issuecomment-3904524433), this is a **greenfield implementation** with an estimated 2.5-4 weeks effort. The core architectural decision is whether to build a custom canvas-based editor or leverage an existing Flutter package.

## Decision Drivers

* **Time to Market:** 2.5-4 week timeline requires leveraging existing solutions
* **Maintainability:** Minimize custom canvas logic that's error-prone and hard to test
* **Platform Alignment:** Must integrate seamlessly with Flutter/BLoC architecture
* **Feature Completeness:** Package must support corner management, snapping, and room controls
* **Data Persistence:** Must integrate with Python Flask backend and Firebase storage
* **Performance:** Canvas rendering must be smooth on mid-to-low-end Android devices (target: 60fps)

## Considered Options

### Option 1: Use `floor_plan_editor` Package ✅ SELECTED

**Pros:**
- Mature package (pub.dev score: 130+, actively maintained)
- Built-in support for rooms, corners, walls, doors, and windows
- Customizable canvas with gesture controls (drag, pinch-zoom, rotate)
- SVG export/import capabilities
- Reduces custom canvas code by ~80% (estimated 10-12 days saved)
- Proven production use in real estate/surveyor apps

**Cons:**
- Dependency on external package (risk: abandonment or breaking changes)
- Limited customization for corner auto-generation (need custom wrapper logic)
- Package API may not match backend data model 1:1 (requires mapping layer)
- Learning curve for team unfamiliar with package

**Mitigation:**
- Fork package as contingency if maintainer abandons it
- Implement abstraction layer (`FloorPlanEditorService`) to decouple app from package
- Comprehensive widget tests to catch API breaking changes early

### Option 2: Custom Canvas Implementation

**Pros:**
- Full control over features and UX
- No external dependency risk
- Exact match to business requirements

**Cons:**
- **Estimated +2-3 weeks additional development** (24-36 days total)
- High complexity: gesture handling, zoom/pan, collision detection
- Requires specialized canvas/animation expertise (team skill gap)
- Extensive testing required for edge cases
- Higher maintenance burden (canvas bugs, performance optimization)

**Verdict:** Rejected due to timeline and resource constraints.

### Option 3: Hybrid Approach (Custom + Package)

**Pros:**
- Use package for base canvas, extend with custom features

**Cons:**
- Complexity of maintaining fork divergence
- Difficult to merge upstream updates
- Best of neither world: external dependency + custom code burden

**Verdict:** Rejected; violates DRY and adds complexity without clear benefit.

---

## Decision Outcome

**Chosen Option:** **Option 1 - Use `floor_plan_editor` Package**

### Integration Strategy

#### 1. Package Integration
```yaml
# pubspec.yaml
dependencies:
  floor_plan_editor: ^2.0.0  # Lock to major version to prevent breaking changes
  flutter_svg: ^2.0.9
  equatable: ^2.0.5
```

#### 2. App Navigation Flow
```
PropertyDetailsScreen (existing)
  └─> [FAB: "Edit Floor Plan"] 
       └─> FloorPlanEditorScreen (NEW)
            ├─> FloorPlanEditorCanvas (floor_plan_editor package)
            ├─> CornerToolbar (custom - auto-generation)
            ├─> SnappingController (custom - snap logic)
            └─> [Save Button]
                 └─> API POST → Backend
                 └─> Firebase Storage (SVG)
                 └─> Navigator.pop() → PropertyDetailsScreen
```

#### 3. Storage Strategy: **Hybrid (Backend Primary + Firebase Backup)**

**Decision:** Store structured JSON in Flask backend, SVG export in Firebase Storage.

**Rationale:**
- **Backend (PostgreSQL/MongoDB):** Source of truth for structured data (corners, rooms, metadata)
  - Enables server-side validation, search, analytics
  - Required for multi-device sync and conflict resolution
  - Better query performance for property listings with floor plans
  
- **Firebase Storage:** SVG/PNG export for viewing and sharing
  - Fast CDN delivery for image rendering
  - Cheaper storage for large binary files
  - Enables offline-first architecture (sync when online)

**Data Flow:**
1. User edits floor plan → FloorPlanBloc captures changes
2. On Save → `POST /api/v1/properties/{id}/floor_plans` (JSON data)
3. Backend returns `floor_plan_id` → Export SVG → Firebase Storage
4. Store Firebase URL in backend `floor_plan.svg_url` field

**Alternative (Rejected):** Firebase-only storage
- **Con:** Difficult to query/aggregate floor plan data
- **Con:** No backend validation or business logic enforcement
- **Con:** Harder to integrate with existing property management workflows

---

## Architecture Components

### High-Level System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    casit-app (Flutter)                      │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         PropertyDetailsScreen                       │  │
│  │  ┌────────────────────────────────────┐             │  │
│  │  │  [Edit Floor Plan FAB]             │             │  │
│  │  └────────────┬───────────────────────┘             │  │
│  └───────────────┼─────────────────────────────────────┘  │
│                  │                                         │
│                  ▼                                         │
│  ┌─────────────────────────────────────────────────────┐  │
│  │      FloorPlanEditorScreen                          │  │
│  │  ┌──────────────────────────────────────────────┐   │  │
│  │  │  FloorPlanBloc (State Management)            │   │  │
│  │  │  - FloorPlanLoadedState                      │   │  │
│  │  │  - FloorPlanEditingState                     │   │  │
│  │  │  - FloorPlanSavingState                      │   │  │
│  │  └──────────────┬───────────────────────────────┘   │  │
│  │                 │                                    │  │
│  │  ┌──────────────▼───────────────────────────────┐   │  │
│  │  │  FloorPlanEditorCanvas                       │   │  │
│  │  │  (floor_plan_editor package)                 │   │  │
│  │  │  - Room rendering                            │   │  │
│  │  │  - Corner drag/drop                          │   │  │
│  │  │  - Wall drawing                              │   │  │
│  │  └──────────────┬───────────────────────────────┘   │  │
│  │                 │                                    │  │
│  │  ┌──────────────▼───────────────────────────────┐   │  │
│  │  │  Custom Features (Wrappers)                  │   │  │
│  │  │  - CornerAutoGenerator                       │   │  │
│  │  │  - SnappingController                        │   │  │
│  │  │  - RoomTransformHandler                      │   │  │
│  │  └──────────────┬───────────────────────────────┘   │  │
│  └─────────────────┼─────────────────────────────────┘  │
└────────────────────┼───────────────────────────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │  FloorPlanRepository    │
        │  (Data Layer)           │
        └────────┬────────────────┘
                 │
       ┌─────────┴─────────┐
       │                   │
       ▼                   ▼
┌──────────────┐   ┌────────────────┐
│ Flask API    │   │ Firebase       │
│ Backend      │   │ Storage        │
│ (JSON data)  │   │ (SVG/PNG)      │
└──────────────┘   └────────────────┘
```

### State Management: BLoC Pattern

```dart
// floor_plan_bloc.dart
class FloorPlanBloc extends Bloc<FloorPlanEvent, FloorPlanState> {
  final FloorPlanRepository repository;
  
  FloorPlanBloc({required this.repository}) : super(FloorPlanInitial()) {
    on<LoadFloorPlan>(_onLoadFloorPlan);
    on<UpdateCorner>(_onUpdateCorner);
    on<AddRoom>(_onAddRoom);
    on<DeleteRoom>(_onDeleteRoom);
    on<EnableSnapping>(_onEnableSnapping);
    on<RotateRoom>(_onRotateRoom);
    on<SaveFloorPlan>(_onSaveFloorPlan);
    on<UndoAction>(_onUndoAction);
    on<RedoAction>(_onRedoAction);
  }
}

// States
abstract class FloorPlanState extends Equatable {}
class FloorPlanInitial extends FloorPlanState {}
class FloorPlanLoading extends FloorPlanState {}
class FloorPlanLoaded extends FloorPlanState {
  final FloorPlan floorPlan;
  final List<FloorPlan> undoStack;
  final List<FloorPlan> redoStack;
}
class FloorPlanSaving extends FloorPlanState {}
class FloorPlanSaved extends FloorPlanState {}
class FloorPlanError extends FloorPlanState {
  final String message;
}

// Events
abstract class FloorPlanEvent extends Equatable {}
class LoadFloorPlan extends FloorPlanEvent {
  final String propertyId;
  final String? floorPlanId;
}
class UpdateCorner extends FloorPlanEvent {
  final String cornerId;
  final Offset newPosition;
  final double? value;
}
class SaveFloorPlan extends FloorPlanEvent {
  final FloorPlan floorPlan;
}
```

---

## Trade-offs Analysis

### Accepted Trade-offs

1. **External Dependency Risk** → **Acceptable**
   - Mitigation: Abstraction layer + fork contingency plan
   - Benefit: 10-12 days faster delivery

2. **Package API Mismatch** → **Manageable**
   - Mitigation: Data mapping layer in repository
   - Benefit: Package handles complex canvas rendering

3. **Limited Auto-Generation Customization** → **Solvable**
   - Mitigation: Custom wrapper `CornerAutoGenerator`
   - Benefit: Don't reinvent canvas wheel

### Rejected Trade-offs

1. **Firebase-Only Storage** → Too limiting for backend queries
2. **Custom Canvas** → Timeline violation (2-3 weeks overhead)
3. **No Undo/Redo** → Poor UX for complex editing tasks

---

## Consequences

### Positive

- **Faster Time to Market:** 2.5-4 weeks vs 5-7 weeks (custom)
- **Lower Maintenance:** Package handles canvas complexity
- **Better UX:** Proven gestures and interactions
- **Testability:** Widget tests vs complex canvas mocking

### Negative

- **Learning Curve:** Team must learn `floor_plan_editor` API (~2-3 days)
- **Package Constraints:** May need workarounds for edge cases
- **Breaking Changes:** Future package updates may require refactoring

### Neutral

- **Hybrid Storage:** More moving parts, but better architecture
- **Abstraction Layer:** Extra code, but cleaner separation of concerns

---

## Implementation Risks and Mitigations

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Package abandonment | Low (20%) | High | Fork repository + 6-month runway |
| Breaking API changes | Medium (40%) | Medium | Lock major version + abstraction layer |
| Performance on low-end devices | Medium (30%) | High | Performance profiling + optimization phase |
| Complex snapping logic bugs | High (60%) | Medium | Unit tests + iterative refinement |
| Backend API schema misalignment | Low (15%) | Medium | Early API contract review with @BackendLead |

---

## Compliance and Standards

- **Code Style:** Flutter/Dart official style guide + casit-app linting rules
- **State Management:** BLoC pattern (aligned with existing app architecture)
- **Testing:** 70% coverage target (unit + widget + integration tests)
- **API Versioning:** `/api/v1/` prefix for backend endpoints
- **Data Privacy:** Floor plan data classified as property metadata (GDPR compliant)

---

## References

- [floor_plan_editor Package](https://pub.dev/packages/floor_plan_editor)
- [@Atlas Technical Assessment](https://github.com/Ghabs95/agents/issues/4#issuecomment-3904524433)
- [Case Italia Workflows](../WORKFLOWS.md)
- [Flutter BLoC Documentation](https://bloclibrary.dev/)

---

## Approval

- [x] @Architect (Principal Software Architect)  
- [ ] @MobileLead (Implementation Review)  
- [ ] @BackendLead (API Contract Review)  
- [ ] @QAGuard (Testing Strategy Review)  

**Next Steps:**
1. Review by @MobileLead → Implementation breakdown
2. Backend API contract definition by @BackendLead
3. UX design by @ProductDesigner → UI/UX mockups
