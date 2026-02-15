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
- [ ] **Step 2: Technical Feasibility** (@Atlas)
  - **Owner:** @Atlas (Technical Director)
  - **Output Required:** Technical assessment of HOW and WHEN
  - **Status:** Routed to @Atlas - Awaiting response
  - **GitHub:** Routing comment posted at https://github.com/Ghabs95/agents/issues/4#issuecomment-3904491656

### ⏳ Pending Steps
- [ ] **Step 2: Technical Feasibility** (@Atlas) - HOW and WHEN
- [ ] **Step 3: Architecture Design** (@Architect) - ADR + breakdown
- [ ] **Step 4: UX Design** (@ProductDesigner) - Wireframes
- [ ] **Step 5: Implementation** (Tier 2 Lead) - Code + tests
- [ ] **Step 6: Quality Gate** (@QAGuard) - Coverage check
- [ ] **Step 7: Compliance Gate** (@Privacy) - PIA (if user data)
- [ ] **Step 8: Deployment** (@OpsCommander) - Production deploy
- [ ] **Step 9: Documentation** (@Scribe) - Changelog + docs

## Next Actions

### For @Atlas (Technical Feasibility)
**Questions to assess:**
1. **HOW:** 
   - Where is the surveyor tool code located? (casit-app, casit-be, or both?)
   - What UI framework is used for the floor plan editor?
   - Are there existing snapping/attachment algorithms?
   - What's the architecture for corner/room manipulation?

2. **WHEN:**
   - Estimate effort for each feature
   - Dependencies (if any)
   - Recommended implementation order
   - Risk assessment

3. **Technical Questions:**
   - Does the tool use canvas, SVG, or a library (e.g., Konva, Fabric.js)?
   - Are corners/rooms stored as data models or DOM elements?
   - Is there existing geometry/collision detection code?

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
**Last Updated:** 2026-02-15T13:58:00Z
**Tracked by:** @ProjectLead
**Current Owner:** @Atlas
