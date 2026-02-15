# ✨ Feature: Surveyor Review
**Project:** Case Italia
**Type:** feature
**Status:** Step 4 Complete - UX Design Approved
**Workflow:** New Feature (Full SOP)

## Overview
Review and enhance the surveyor tool (property floor plan editor) for Case Italia.

**Context:** The surveyor tool is a floor plan editor allowing users to create and edit property layouts with rooms, doors, windows, and corners. This enhancement addresses specific UX and functionality issues.

## SOP Progress

Following the **New Feature** workflow from WORKFLOWS.md:

```
@Ghabs → @Atlas → @Architect → @ProductDesigner → [Tier 2 Lead] → @QAGuard → @Privacy → @OpsCommander → @Scribe
```

### ✅ Completed Steps
- [x] Task created and routed by @ProjectLead
- [x] **Step 1: Vision & Scope** (@Ghabs) - COMPLETED
  - **Input Received:** Feature requirements defined
  - **Scope:** Enhancement of existing surveyor floor plan editor
- [x] **Step 2: Technical Feasibility** (@Atlas) - COMPLETED
  - **Output:** Comprehensive technical assessment delivered
  - **Key Finding:** Greenfield implementation required (surveyor doesn't exist yet)
  - **Recommendation:** Use floor_plan_editor package, 2.5-4 weeks effort
  - **GitHub:** Assessment posted at https://github.com/Ghabs95/agents/issues/4#issuecomment-3904524433
- [x] **Step 3: Architecture Design** (@Architect) - COMPLETED ✅
  - **Output:** Complete ADR + technical breakdown delivered
  - **Decision:** Use floor_plan_editor package v2.0.0 with abstraction layer
  - **Architecture:** Flutter BLoC pattern, Firebase Storage, Python Flask API
  - **Timeline:** 13-21 days implementation (2.5-4 weeks)
  - **Deliverables:**
    - ✅ ADR-001: Floor Plan Editor Architecture
    - ✅ Component architecture diagram
    - ✅ API contracts (REST endpoints + schemas)
    - ✅ Data models (Mobile + Backend)
    - ✅ Implementation breakdown (22 days of tasks)
  - **Approval:** @Atlas approved 2026-02-15
  - **GitHub:** Design posted at https://github.com/Ghabs95/agents/issues/4
- [x] **Step 4: UX Design** (@ProductDesigner) - COMPLETED ✅
  - **Output:** Complete wireframes & interaction specifications delivered
  - **Deliverables:**
    - ✅ Floor Plan Editor wireframes (casit-agents/docs/ux/floor-plan-editor-wireframes.md)
    - ✅ Screen layout: AppBar, Canvas, Toolbar with 6 tools
    - ✅ Component breakdown: All UI elements specified
    - ✅ Interaction specifications: Gestures, auto-generation, snapping
    - ✅ Panels & Dialogs: Corner properties, Door/Window properties, Unsaved changes
    - ✅ Accessibility: WCAG 2.1 AA compliance verified (48dp touch targets, 7.2:1 contrast)
    - ✅ Material Design 3 alignment: Yellow500 primary, Grey palette, responsive typography
    - ✅ Edge cases: Empty, loading, error, performance states
    - ✅ Animations: Micro-interactions (200-500ms), gesture feedback
    - ✅ Implementation checklist: 6 phases (21 days estimated)
  - **CTO Approval:** @Atlas approved 2026-02-15T15:38:00Z
  - **Technical Feasibility:** ✅ APPROVED
    - Interaction specifications technically sound and achievable
    - Performance targets realistic (60fps on Snapdragon 665)
    - Accessibility compliance verified
    - API/backend integration requirements satisfied
  - **GitHub:** UX design posted at https://github.com/Ghabs95/agents/issues/4

## Feature Requirements (from @Ghabs)

### WHAT - Feature List
1. **Corner Management:**
   - Add two new corners near openings (door and window)
   - Fix: Setting corner values doesn't work correctly

2. **Snapping & Attachment:**
   - Join two touching corners
   - Dragging a door to the closest corner will attach it there

3. **Room Controls:**
   - Improve room rotation
   - Improve room movement

### WHY - User Problem
The current surveyor tool has UX friction in floor plan editing:
- Corners near openings require manual placement
- Elements don't snap/attach automatically (poor DX)
- Room manipulation (rotation/movement) needs refinement
- Corner value editing is broken

## Architecture Summary (@Architect)

### Technical Stack
- **Mobile:** Flutter 3.19+ with `floor_plan_editor: ^2.0.0` package
- **State Management:** BLoC pattern (FloorPlanEditorBloc)
- **Backend:** Python Flask REST API
- **Storage:** Firebase Storage (SVG exports), PostgreSQL (metadata)
- **Integration:** Abstraction layer (FloorPlanEditorService) for package decoupling

### Key Decisions (ADR-001)
1. **Package Selection:** floor_plan_editor (vs custom canvas)
   - Saves ~10-12 days development time
   - Mature, actively maintained package
   - Mitigation: Abstraction layer for decoupling
2. **Data Flow:** Mobile ↔️ Backend API ↔️ PostgreSQL + Firebase Storage
3. **Performance Targets:** 60fps mobile, p95 < 200ms API latency

### Implementation Phases
- **Phase 1:** Foundation (7 days) - Package integration, models, API scaffolding
- **Phase 2:** Core Features (8 days) - Editor screen, CRUD operations, corner logic
- **Phase 3:** Advanced Features (5 days) - Snapping, rotation, performance optimization
- **Phase 4:** Testing & Polish (2 days) - Integration tests, accessibility

**Total Effort:** 13-21 days across @MobileLead, @BackendLead, @CloudArch

### 🔄 Current Step
- [ ] **Step 5: Implementation** (Tier 2 Lead)
  - **Owner:** @MobileLead + @BackendLead
  - **Status:** NEXT - Ready to begin implementation
  - **CTO Approval:** UX Design approved 2026-02-15T15:38:00Z
  - **Input:** Complete UX wireframes + architecture design
  - **Resources:**
    - **UX Wireframes:** `docs/ux/floor-plan-editor-wireframes.md`
    - **ADR-001:** `docs/architecture/ADR-001-floor-plan-editor-architecture.md`
    - **Component Architecture:** `docs/architecture/floor-plan-component-architecture.md`
    - **Data Models:** `docs/architecture/floor-plan-data-models.md`
    - **API Contracts:** `docs/architecture/floor-plan-api-contracts.md`
    - **Implementation Breakdown:** `docs/architecture/floor-plan-implementation-breakdown.md`
  - **Estimated Effort:** 13-21 days (2.5-4 weeks)
  - **Implementation Phases:**
    1. Foundation (Days 1-3): AppBar, Canvas, Toolbar, Grid
    2. Core Features (Days 4-8): Room creation, corners, move/rotate
    3. Openings (Days 9-12): Doors, windows, auto-generation
    4. Snapping (Days 13-15): Detection, indicators, haptics
    5. Polish (Days 16-18): Dialogs, export, undo, error states
    6. Accessibility (Days 19-21): Screen readers, audit, help docs

### ⏳ Pending Steps
- [ ] **Step 6: Quality Gate** (@QAGuard) - Coverage check
- [ ] **Step 7: Compliance Gate** (@Privacy) - PIA (if user data)
- [ ] **Step 8: Deployment** (@OpsCommander) - Production deploy
- [ ] **Step 9: Documentation** (@Scribe) - Changelog + docs

## Next Actions

### For @Architect (Architecture Design)
**Input from @Atlas:**
- ✅ **Verdict:** Greenfield implementation feasible (2.5-4 weeks)
- ✅ **Recommendation:** Use `floor_plan_editor` Flutter package
- ✅ **Effort Breakdown:** Base editor (5-7d) + Features (8-14d)
- ✅ **Data Model:** Proposed FloorPlan/Room/Corner/Opening entities

**Tasks for @Architect:**
1. **ADR Creation:**
   - Document decision: floor_plan_editor vs custom canvas
   - Define integration approach with existing casit-app
   - Storage strategy (Firebase? Backend API?)

2. **Technical Breakdown:**
   - Detailed component architecture
   - Data model schema (match backend needs)
   - API contracts for floor plan CRUD
   - State management strategy (BLoC pattern alignment)

3. **Dependencies:**
   - Backend API requirements
   - Firebase Storage setup (for SVG export)
   - Package version constraints

### For Future Steps:
- **@ProductDesigner:** Use Atlas assessment to inform UX design
- **@MobileLead:** Review floor_plan_editor package compatibility
- **@BackendLead:** Plan API endpoints for floor plan persistence

### Success Criteria
- Corners auto-generate near doors/windows
- Drag-and-drop elements snap to nearest valid corner
- Room rotation/movement is smooth and intuitive
- Corner value editing works reliably

## Links
- **Issue:** https://github.com/Ghabs95/agents/issues/4
- **Workflow:** WORKFLOWS.md → New Feature

---
**Created:** 2026-02-15
**Last Updated:** 2026-02-15T15:38:41Z
**Tracked by:** @ProjectLead
**Current Owner:** @MobileLead + @BackendLead
**Previous Owners:** @Ghabs (Vision) → @Atlas (Feasibility) → @Architect (Architecture) → @ProductDesigner (UX Design) → @Atlas (CTO Approval)
