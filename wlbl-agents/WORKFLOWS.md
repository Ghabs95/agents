# WALLIBLE — Standard Operating Procedures (SOPs)

> **Owner:** `@ProjectLead`
> **Approver:** `@Atlas`
>
> These workflows define **how work flows through the agent organization**. Every agent involved in a chain should follow these steps in order. `@ProjectLead` orchestrates routing; domain owners make decisions.

---

## Table of Contents

1. [New Feature](#1-new-feature)
2. [Bug Fix](#2-bug-fix)
3. [Release](#3-release)
4. [Incident Response](#4-incident-response)
5. [Cross-Project Change](#5-cross-project-change)
6. [Compliance Review](#6-compliance-review)

---

## 1. New Feature

**Trigger:** Vision from `@Ghabs`, request from `@Business`, or proposal from any agent.

```
@Ghabs ──▶ @Atlas ──▶ @Architect ──▶ @ProductDesigner ──▶ [Tier 2 Lead] ──▶ @QAGuard ──▶ @Privacy ──▶ @OpsCommander ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Vision & Scope** | `@Ghabs` | Feature brief: WHAT and WHY | Founder's Check (4 questions) |
| 2 | **Technical Feasibility** | `@Atlas` | Feasibility assessment, HOW and WHEN | Rejects if infeasible or misaligned |
| 3 | **Architecture Design** | `@Architect` | ADR, data flow diagram, project breakdown | Mass Adoption Standard (100k users) |
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
- **Funnel-related feature** → `@WebDirector` owns the strategy; `@FrontendLead` + `@ContentLead` implement.

### Skip conditions

- Steps 4 (UX) can be skipped for backend-only or infra-only changes.
- Step 7 (Compliance) is **mandatory** if the feature touches user data, payments, or analytics. `@ProjectLead` flags this.

---

## 2. Bug Fix

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
- **Payment bug** → `@Privacy` + `@QuantArchitect` jointly investigate.

---

## 3. Release

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
| 9 | **Post-Release Metrics** | `@GrowthLead` | Monitor key metrics for 24h | No regression in AARRR metrics |
| 10 | **Announce** | `@Scribe` + `@ContentLead` | Release announcement (atmos blog, app store notes) | — |

### Release types

| Type | QA Depth | Privacy Review | Approval |
|---|---|---|---|
| **Major** (new feature, breaking change) | Full suite | Mandatory | `@Ghabs` |
| **Minor** (improvements, non-breaking) | Affected projects only | If data-touching | `@Atlas` |
| **Hotfix** (critical bug) | Targeted tests only | If security-related | `@Atlas` (fast-track) |

---

## 4. Incident Response

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

---

## 5. Cross-Project Change

**Trigger:** API contract change, shared design token update, data model migration, or dependency upgrade.

```
[Requestor] ──▶ @Architect ──▶ [All Affected Leads] ──▶ @QAGuard ──▶ @Privacy ──▶ @OpsCommander ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Propose** | Any Tier 2 Lead | Change proposal: what, why, which projects affected | — |
| 2 | **Impact Analysis** | `@Architect` | Affected projects list, breaking change assessment, migration plan | Mass Adoption Standard |
| 3 | **Design** | `@Architect` + affected leads | Updated ADR, API contract, or data schema | All leads agree on the contract |
| 4 | **Implement (parallel)** | Each affected Tier 2 Lead | Code changes in their project | Backward compatibility maintained during rollout |
| 5 | **Contract Tests** | `@QAGuard` | Cross-project integration tests pass | API contract tests ✅ |
| 6 | **Compliance Check** | `@Privacy` | If data model changed: privacy impact assessment | Can block |
| 7 | **Coordinated Deploy** | `@OpsCommander` | Deploy in correct order (backend first, consumers second) | Zero-downtime |
| 8 | **Document** | `@Scribe` | Updated API docs, architecture diagrams, changelog | — |

### Cross-project patterns

| Change Type | Coordination | Deploy Order |
|---|---|---|
| **API contract change** | `@BackendLead` + `@MobileLead` + `@FrontendLead` | Backend → Frontend → Mobile |
| **Design token update** | `@ProductDesigner` + `@FrontendLead` + `@MobileLead` + `@ContentLead` | Design system → all consumers |
| **Data model migration** | `@BackendLead` + `@QuantArchitect` + `@CloudArch` | DB migration → Backend → Functions |
| **Shared dependency upgrade** | `@Architect` + all affected leads | Coordinated, test each project |

### The backward compatibility rule

> **Never break a consumer.**
>
> When changing a shared contract (API, data model, design tokens):
> 1. Add the new version alongside the old.
> 2. Migrate all consumers.
> 3. Remove the old version.
>
> `@Architect` enforces this. `@QAGuard` verifies it.

---

## 6. Compliance Review

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

---

## Workflow Selection Guide

When a task arrives, `@ProjectLead` selects the appropriate workflow:

| Signal | Workflow |
|---|---|
| "Build this feature" / "Add this capability" | [New Feature](#1-new-feature) |
| "This is broken" / "Users report X" | [Bug Fix](#2-bug-fix) |
| "Let's ship" / "Prepare release" | [Release](#3-release) |
| Monitoring alert / service down | [Incident Response](#4-incident-response) |
| "Change the API" / "Update shared tokens" | [Cross-Project Change](#5-cross-project-change) |
| "Audit this" / quarterly schedule | [Compliance Review](#6-compliance-review) |

---

*These SOPs are living documents. `@Atlas` approves changes. `@Scribe` maintains version history.*
