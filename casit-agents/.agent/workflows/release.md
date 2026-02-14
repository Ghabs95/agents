---
description: Release Workflow
---
# Release

**Trigger:** Sprint completion, milestone reached, or `@Ghabs` go decision.

```
@Atlas ──▶ @QAGuard ──▶ @Privacy ──▶ @Scribe ──▶ @OpsCommander ──▶ @GrowthLead
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Release Decision** | `@Atlas` | Release scope, code freeze signal | Aligns with `@Ghabs` roadmap |
| 2 | **Code Freeze** | All Tier 2 Leads | Feature branches merged, no new commits | `@ProjectLead` confirms all work merged |
| 3 | **Full QA Pass** | `@QAGuard` | Cross-project test report | All projects ≥ coverage thresholds |
| 4 | **Pre-Release Audit** | `@Privacy` | Compliance sign-off | Can block release |
| 5 | **Changelog & Docs** | `@Scribe` | CHANGELOG.md, release notes, API docs | — |
| 6 | **Staging Deploy** | `@OpsCommander` | Staging environment verified | Smoke tests pass |
| 7 | **Go/No-Go** | `@Atlas` → `@Ghabs` | Final approval | Founder's Check |
| 8 | **Production Deploy** | `@OpsCommander` | Released to production | Zero-downtime |
| 9 | **Post-Release Metrics** | `@GrowthLead` | Monitor key metrics for 24h | No regression in core KPIs |
| 10 | **Announce** | `@Scribe` | Release announcement | — |

### Release types

| Type | QA Depth | Privacy Review | Approval |
|---|---|---|---|
| **Major** (new feature, breaking change) | Full suite | Mandatory | `@Ghabs` |
| **Minor** (improvements, non-breaking) | Affected projects only | If data-touching | `@Atlas` |
| **Hotfix** (critical bug) | Targeted tests only | If security-related | `@Atlas` (fast-track) |
