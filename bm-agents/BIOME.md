# BIOME — Agent Organization

The AI team behind **BIOME**, the biohacking and health optimization ecosystem.

This repo centralizes all GitHub Copilot custom agents that govern the BIOME ecosystem. Each agent is a persistent persona with scoped responsibilities, delegation authority, and collaboration rules.

## The Ecosystem

| Repo | Agent | Tech Stack | Purpose |
|---|---|---|---|
| `biome/backend` | `@BackendLead` | Java, Spring Boot, MySQL | Core backend API |
| `biome/backend-py` | `@BackendLead` | Python | Supporting python backend services |
| `biome/api-data` | `@BackendLead` | Python | Data processing API |
| `biome/back-office` | `@FrontendLead` | React, Vite, TS | Admin panel for managing content/users |
| `biome/pulse` | `@QuantArchitect` | Python | Health data algorithms & processing |
| `biome/iac-*` | `@CloudArch` | Terraform, AWS | Infrastructure as Code |
| `bm-agents` | All agents | — | This folder |

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
      @Architect  @Ops    @Scribe   [App]    @Product   @Project  @Growth
          │      Cmdr      (Docs)   [Devs]    Designer    Lead      Lead
          │
          ├── @BackendLead     (backend, api-data)
          ├── @CloudArch       (infrastructure)
          ├── @QuantArchitect  (pulse)
          ├── @FrontendLead    (back-office)
          └── @QAGuard         (cross-project)
```

## Governance

- **The Golden Rule:** Ghabs decides WHAT and WHY. Atlas decides HOW and WHEN.
- **Audit Independence:** `@Privacy` reports to `@Ghabs`, not `@Atlas` — the auditor never reports to the builder.
- **Privacy Gate:** No growth experiment ships without `@Privacy` review.
- **Founder's Check:** Every major release must pass 4 questions: needle-moving, quality, sustainable, mission-aligned.

## Usage

1. Open this repo as part of your **VS Code multi-root workspace** alongside the BIOME project repos.
2. Type `@AgentName` in GitHub Copilot Chat to invoke any agent.
3. Agents reach into all repos via `codebase` and `search` tools.

### Quick examples

| You want to... | Invoke |
|---|---|
| Design a new feature flow | `@Ghabs` or `@Atlas` |
| Add a backend endpoint | `@BackendLead` |
| Update health algorithms | `@QuantArchitect` |
| Update the backoffice UI | `@FrontendLead` |
| Check test coverage | `@QAGuard` |
| Audit data handling | `@Privacy` |
| Route a task | `@ProjectLead` |
| Deploy infrastructure | `@CloudArch` |

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
| `backend.agent.md` | `@BackendLead` | Java/Python Backend Lead |
| `cloud-arch.agent.md` | `@CloudArch` | Infrastructure Architect |
| `quant-architect.agent.md` | `@QuantArchitect` | Pulse / Algorithms Lead |
| `frontend.agent.md` | `@FrontendLead` | React / Backoffice Lead |
| `qa-guard.agent.md` | `@QAGuard` | Quality Assurance Guardian |

## Workflows

See [WORKFLOWS.md](WORKFLOWS.md) for the detailed Standard Operating Procedures (SOPs).
