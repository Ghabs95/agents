---
description: Cross-Project Change Workflow
---
# Cross-Project Change

**Trigger:** API contract change, shared design token update, data model migration, or dependency upgrade.

```
[Requestor] ──▶ @Architect ──▶ [All Affected Leads] ──▶ @QAGuard ──▶ @Privacy ──▶ @OpsCommander ──▶ @Scribe
```

### Steps

| # | Step | Owner | Output | Gate |
|---|---|---|---|---|
| 1 | **Propose** | Any Tier 2 Lead | Change proposal: what, why, which projects affected | — |
| 2 | **Impact Analysis** | `@Architect` | Affected projects list, breaking change assessment, migration plan | Scale Standard |
| 3 | **Design** | `@Architect` + affected leads | Updated ADR, API contract, or data schema | All leads agree on the contract |
| 4 | **Implement (parallel)** | Each affected Tier 2 Lead | Code changes in their project | Backward compatibility maintained during rollout |
| 5 | **Contract Tests** | `@QAGuard` | Cross-project integration tests pass | API contract tests ✅ |
| 6 | **Compliance Check** | `@Privacy` | If data model changed: privacy impact assessment | Can block |
| 7 | **Coordinated Deploy** | `@OpsCommander` | Deploy in correct order (backend first, consumers second) | Zero-downtime |
| 8 | **Document** | `@Scribe` | Updated API docs, architecture diagrams, changelog | — |

### Cross-project patterns

| Change Type | Coordination | Deploy Order |
|---|---|---|
| **API contract change** | `@BackendLead` + `@MobileLead` | Backend → Mobile |
| **Data model migration** | `@BackendLead` + `@DataPrepLead` + `@CloudArch` | Data prep → Backend → Mobile |
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
