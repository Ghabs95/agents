---
description: Incident Response Workflow
---
# Incident Response

**Trigger:** Monitoring alert, user reports, or system failure.

```
[Alert] ──▶ @OpsCommander ──▶ @Atlas ──▶ [Tier 2 Lead] ──▶ @Privacy ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Detect** | `@OpsCommander` (or monitoring) | Alert with affected service, error rate, user impact | — |
| 2 | **Assess Severity** | `@OpsCommander` | Incident level: SEV1 (down) / SEV2 (degraded) / SEV3 (minor) | — |
| 3 | **Notify** | `@OpsCommander` | `@Atlas` notified; if SEV1, `@Ghabs` notified | — |
| 4 | **Diagnose** | Tier 2 Lead + `@OpsCommander` | Root cause identified | — |
| 5 | **Mitigate** | `@OpsCommander` | Service restored (rollback, hotfix, or workaround) | — |
| 6 | **Breach Assessment** | `@Privacy` | If user data exposed: GDPR Art. 33 (72h notification clock starts) | Mandatory if data involved |
| 7 | **Permanent Fix** | Tier 2 Lead | Code fix + test → follows Bug Fix workflow (steps 4–7) | — |
| 8 | **Post-Mortem** | `@Atlas` + `@OpsCommander` | Incident report: timeline, root cause, remediation, prevention | — |
| 9 | **Document** | `@Scribe` | Post-mortem archived, runbook updated | — |

### Incident severity

| Level | Definition | Response | Communication |
|---|---|---|---|
| **SEV1** | Service down, all users affected | Immediate, all-hands | `@Ghabs` notified |
| **SEV2** | Service degraded, partial impact | < 1h response | `@Atlas` notified |
| **SEV3** | Minor issue, workaround available | Next business day | `@ProjectLead` tracks |

### Key rules

- **Mitigate first, investigate second.** Restore service before root-causing.
- **No blame.** Post-mortems focus on systems, not people.
- **Every SEV1/SEV2 produces a post-mortem.** No exceptions.
