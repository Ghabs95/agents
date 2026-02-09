---
description: New Feature Workflow
---
# New Feature

**Trigger:** Vision from `@Ghabs`, request from `@Business`, or proposal from any agent.

```
@Ghabs ──▶ @Atlas ──▶ @Architect ──▶ @ProductDesigner ──▶ [Tier 2 Lead] ──▶ @QAGuard ──▶ @Privacy ──▶ @OpsCommander ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Vision & Scope** | `@Ghabs` | Feature brief: WHAT and WHY | Founder's Check (4 questions) |
| 2 | **Technical Feasibility** | `@Atlas` | Feasibility assessment, HOW and WHEN | Rejects if infeasible or misaligned |
| 3 | **Architecture Design** | `@Architect` | ADR, data flow diagram, project breakdown | — |
| 4 | **UX Design** | `@ProductDesigner` | Wireframes, interaction spec | Mobile-first, WCAG 2.1 AA |
| 5 | **Implementation** | Tier 2 Lead(s) | Code, unit tests | Project-specific standards |
| 6 | **Quality Gate** | `@QAGuard` | Test coverage report, regression check | Coverage ≥ threshold per project |
| 7 | **Compliance Gate** | `@Privacy` | Privacy impact assessment (if user data involved) | Can block release |
| 8 | **Deployment** | `@OpsCommander` | Production deploy | Zero-downtime, blue/green |
| 9 | **Documentation** | `@Scribe` | Changelog, API docs, README updates | — |

### Routing rules

- **Single-project feature** → `@ProjectLead` routes to the correct Tier 2 lead after step 3.
- **Multi-project feature** → `@Architect` breaks it down and coordinates across leads.
- **Growth-related feature** → `@GrowthLead` defines metrics; `@Privacy` reviews tracking.
- **Backoffice feature** → `@FrontendLead` owns implementation.

### Skip conditions

- Steps 4 (UX) can be skipped for backend-only or infra-only changes.
- Step 7 (Compliance) is **mandatory** if the feature touches user data, payments, or analytics. `@ProjectLead` flags this.
