# CASE ITALIA — Agent Organization

The AI team behind **CASE ITALIA**, the real estate data and services ecosystem.

This repo centralizes 15 GitHub Copilot custom agents that govern the CASE ITALIA ecosystem. Each agent is a persistent persona with scoped responsibilities, delegation authority, and collaboration rules.

## The Ecosystem

| Repo | Agent | Tech Stack | Purpose |
|---|---|---|---|
| `casit-omi` | `@DataPrepLead` | Python, Scrapy, Geo data | OMI data preparation and zone mapping |
| `casit-be` | `@BackendLead` | Python, API, Docker | Backend API and services |
| `casit-app` | `@MobileLead` | Flutter / Dart | Client mobile app |
| `casit-agents` | All agents | — | This repo |

## Org Chart

```
                         @Ghabs (CEO)
                             │
              ┌──────────────┼──────────────┐
              │              │              │
          @Business      @Atlas         @Privacy
          (Advisor)      (CTO)        (CARO/DPO)
                             │
          ┌────────┬─────────┼─────────┬──────────┬──────────┬──────────┐
          │        │         │         │          │          │          │
      @Architect  @Ops    @Scribe  @Product  @Project   @Growth
          │      Cmdr      (Docs)  Designer   Lead       Lead
          │
          ├── @MobileLead      (casit-app)
          ├── @BackendLead     (casit-be)
          ├── @DataPrepLead    (casit-omi)
          ├── @CloudArch       (infrastructure)
          └── @QAGuard         (cross-project)
```

## Governance

- **The Golden Rule:** Ghabs decides WHAT and WHY. Atlas decides HOW and WHEN.
- **Audit Independence:** `@Privacy` reports to `@Ghabs`, not `@Atlas` — the auditor never reports to the builder.
- **Privacy Gate:** No growth experiment ships without `@Privacy` review.
- **Founder's Check:** Every major release must pass 4 questions: needle-moving, quality, sustainable, mission-aligned.

## Usage

1. Open this repo as part of your **VS Code multi-root workspace** alongside the CASE ITALIA project repos.
2. Type `@AgentName` in GitHub Copilot Chat to invoke any agent.
3. Agents reach into all repos via `codebase` and `search` tools.

### Quick examples

| You want to... | Invoke |
|---|---|
| Design a new feature flow | `@Ghabs` or `@Atlas` |
| Implement a Flutter screen | `@MobileLead` |
| Add a backend endpoint | `@BackendLead` |
| Update OMI data pipelines | `@DataPrepLead` |
| Check test coverage | `@QAGuard` |
| Audit data handling | `@Privacy` |
| Route a task to the right person | `@ProjectLead` |

## Agent Files

All agents live in `.github/agents/`:

| File | Agent | Role |
|---|---|---|
| `ghabs.agent.md` | `@Ghabs` | CEO & Founder |
| `atlas.agent.md` | `@Atlas` | CTO |
| `architect.agent.md` | `@Architect` | Principal Software Architect |
| `business.agent.md` | `@Business` | Strategic Advisor |
| `privacy.agent.md` | `@Privacy` | Chief Audit & Risk Officer / DPO |
| `ops.agent.md` | `@OpsCommander` | SRE & DevOps Lead |
| `scribe.agent.md` | `@Scribe` | Documentation Lead |
| `product-designer.agent.md` | `@ProductDesigner` | Product Designer |
| `project-lead.agent.md` | `@ProjectLead` | Project Orchestrator |
| `growth-lead.agent.md` | `@GrowthLead` | Growth Engineer |
| `mobile-lead.agent.md` | `@MobileLead` | Flutter / Mobile Lead |
| `backend.agent.md` | `@BackendLead` | Python / Backend Lead |
| `data-prep.agent.md` | `@DataPrepLead` | OMI Data Preparation Lead |
| `cloud-arch.agent.md` | `@CloudArch` | Infrastructure Architect |
| `qa-guard.agent.md` | `@QAGuard` | Quality Assurance Guardian |

## Workflows

See [WORKFLOWS.md](WORKFLOWS.md) for the detailed Standard Operating Procedures (SOPs) that define how work flows through the agent organization.

Every agent must follow the defined steps for:
1.  **New Feature**
2.  **Bug Fix**
3.  **Release**
4.  **Incident Response**
5.  **Cross-Project Change**
6.  **Compliance Review**

## Reuse

Want to replicate this organization for another startup? See [AGENT-ORG-PLAYBOOK.md](AGENT-ORG-PLAYBOOK.md) — a complete, generic blueprint with step-by-step instructions for recreating and customizing the full agent hierarchy.
