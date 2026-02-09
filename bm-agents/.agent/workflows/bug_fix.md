---
description: Bug Fix Workflow
---
# Bug Fix

**Trigger:** User report, monitoring alert, or agent discovery during development.

```
[Reporter] ──▶ @ProjectLead ──▶ [Tier 2 Lead] ──▶ @QAGuard ──▶ @OpsCommander ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Report** | Anyone | Bug description: expected vs actual, reproduction steps | — |
| 2 | **Triage** | `@ProjectLead` | Severity (Critical/High/Medium/Low), affected project(s), routed to owner | — |
| 3 | **Root Cause Analysis** | Tier 2 Lead | Root cause identified, fix proposed | — |
| 4 | **Fix** | Tier 2 Lead | Code fix + regression test | Test must cover the exact failure |
| 5 | **Verify** | `@QAGuard` | Regression suite passes, no new failures | Coverage did not decrease |
| 6 | **Deploy** | `@OpsCommander` | Hotfix or standard deploy | — |
| 7 | **Document** | `@Scribe` | Changelog entry | — |

### Severity escalation

| Severity | Response Time | Escalation |
|---|---|---|
| **Critical** | Immediate | `@ProjectLead` → `@Atlas` → `@OpsCommander` (bypass normal queue) |
| **High** | < 24h | `@ProjectLead` → Tier 2 Lead |
| **Medium** | Next sprint | `@ProjectLead` → Tier 2 Lead |
| **Low** | Backlog | `@ProjectLead` tracks |

### Special cases

- **Security bug** → `@Privacy` is notified immediately at step 2. They assess breach risk.
- **Cross-project bug** → `@Architect` coordinates the fix across leads.
- **Financial bug** → `@Privacy` + `@QuantArchitect` jointly investigate.
