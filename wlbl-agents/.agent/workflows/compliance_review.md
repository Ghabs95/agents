---
description: Compliance Review Workflow
---
# Compliance Review

**Trigger:** Scheduled quarterly review, pre-release audit, incident-driven assessment, or new regulation.

```
@Privacy ──▶ [All Projects] ──▶ @Atlas ──▶ @Ghabs
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Scope** | `@Privacy` | Audit scope: which projects, which risk domains | — |
| 2 | **Data Flow Audit** | `@Privacy` + Tier 2 Leads | Data flow map: what data, where stored, who accesses, retention | — |
| 3 | **Risk Assessment** | `@Privacy` | Risk matrix: Likelihood × Impact per finding | — |
| 4 | **Findings Report** | `@Privacy` | List of findings with severity (Critical/High/Medium/Low) | — |
| 5 | **Remediation Plan** | `@Architect` + affected leads | Fix timeline and ownership per finding | — |
| 6 | **Remediation** | Tier 2 Leads | Code/config changes | Each fix verified by `@Privacy` |
| 7 | **Re-Audit** | `@Privacy` | Findings resolved, sign-off | Can block release if Critical/High remain |
| 8 | **Report to CEO** | `@Privacy` → `@Ghabs` | Compliance status summary | — |
| 9 | **Document** | `@Scribe` | Audit record archived | — |

### Audit cadence

| Type | Frequency | Scope |
|---|---|---|
| **Quarterly Review** | Every 3 months | Full ecosystem |
| **Pre-Release Audit** | Before every major release | Release scope only |
| **Incident Audit** | After any security/data incident | Affected systems |
| **Regulation Change** | When new regulation applies | Affected domains |

### Risk domains (per `@Privacy` definition)

| Domain | Key checks |
|---|---|
| **Operational** | CI/CD failures, single points of failure, disaster recovery |
| **Financial** | Stripe flow integrity, billing logic, revenue leakage |
| **Security** | Secrets, CORS, IAM, dependencies (CVEs) |
| **Compliance** | GDPR, PSD2, MiFID II, cookie consent |
| **Reputational** | Data breach exposure, downtime SLA |

### The independence rule

> `@Privacy` findings cannot be overridden by `@Atlas`.
> Only `@Ghabs` can accept risk on Critical/High findings — and must document the rationale.
