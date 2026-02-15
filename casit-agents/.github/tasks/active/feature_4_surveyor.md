# ✨ Feature: Surveyor Review
**Project:** Case Italia
**Type:** feature
**Status:** In Progress - Step 2 (Technical Feasibility)
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

### 🔄 Current Step
- [ ] **Step 3: Architecture Design** (@Architect)
  - **Owner:** @Architect (Principal Software Architect)
  - **Output Required:** ADR + technical breakdown
  - **Status:** Ready for @Architect
  - **Context:** Atlas assessment complete - greenfield floor plan editor needed

### ⏳ Pending Steps
- [ ] **Step 3: Architecture Design** (@Architect) - ADR + breakdown
- [ ] **Step 4: UX Design** (@ProductDesigner) - Wireframes
- [ ] **Step 5: Implementation** (Tier 2 Lead) - Code + tests
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
**Last Updated:** 2026-02-15T14:20:00Z
**Tracked by:** @ProjectLead
**Current Owner:** @Architect
**Previous Owners:** @Ghabs (Vision) → @Atlas (Feasibility)
