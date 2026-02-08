# WALLIBLE — Agent Organization

The AI team behind **WALLIBLE**, the fintech ecosystem for sustainable investing.

This repo centralizes all 18 GitHub Copilot custom agents that govern the WALLIBLE ecosystem. Each agent is a persistent persona with scoped responsibilities, delegation authority, and collaboration rules.

## The Ecosystem

| Repo | Agent | Tech Stack | Purpose |
|---|---|---|---|
| `wlbl-app` | `@MobileLead` | Flutter / Dart | Mobile app — the core product |
| `wlbl-ecos` | `@BackendLead` | Python, Docker, Cloud Run | Backend API engine |
| `wlbl-cosmos` | `@QuantArchitect` | Python, Cloud Functions | ESG calculators, optimizers, Stripe |
| `wlbl-bios` | `@FrontendLead` | Next.js, React, Tailwind | Pricing portal — revenue gateway |
| `wlbl-atmos` | `@ContentLead` | Hugo | Landing page (8 languages) |
| `wlbl-agents` | All 18 agents | — | This repo |

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
      @Architect  @Ops    @Scribe   @WebDir  @Product   @Project  @Growth
          │      Cmdr      (Docs)            Designer    Lead      Lead
          │
          ├── @MobileLead      (wlbl-app)
          ├── @BackendLead     (wlbl-ecos)
          ├── @CloudArch       (infrastructure)
          ├── @QuantArchitect  (wlbl-cosmos)
          ├── @FrontendLead    (wlbl-bios)
          ├── @ContentLead     (wlbl-atmos)
          └── @QAGuard         (cross-project)
```

## Governance

- **The Golden Rule:** Ghabs decides WHAT and WHY. Atlas decides HOW and WHEN.
- **Audit Independence:** `@Privacy` reports to `@Ghabs`, not `@Atlas` — the auditor never reports to the builder.
- **Privacy Gate:** No growth experiment ships without `@Privacy` review.
- **Founder's Check:** Every major release must pass 4 questions: needle-moving, quality, sustainable, mission-aligned.

## Usage

1. Open this repo as part of your **VS Code multi-root workspace** alongside the WALLIBLE project repos.
2. Type `@AgentName` in GitHub Copilot Chat to invoke any agent.
3. Agents reach into all repos via `codebase` and `search` tools.

### Quick examples

| You want to... | Invoke |
|---|---|
| Design a new feature flow | `@Ghabs` or `@Atlas` |
| Implement a Flutter screen | `@MobileLead` |
| Add a backend endpoint | `@BackendLead` |
| Check test coverage | `@QAGuard` |
| Audit data handling | `@Privacy` |
| Route a task to the right person | `@ProjectLead` |
| Update the landing page | `@ContentLead` |
| Review the pricing portal | `@FrontendLead` |

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
| `web-director.agent.md` | `@WebDirector` | Head of Web Experience |
| `product-designer.agent.md` | `@ProductDesigner` | Product Designer |
| `project-lead.agent.md` | `@ProjectLead` | Project Orchestrator |
| `growth-lead.agent.md` | `@GrowthLead` | Growth Engineer |
| `mobile-lead.agent.md` | `@MobileLead` | Flutter / Mobile Lead |
| `backend.agent.md` | `@BackendLead` | Python / Backend Lead |
| `cloud-arch.agent.md` | `@CloudArch` | Infrastructure Architect |
| `quant-architect.agent.md` | `@QuantArchitect` | Quant / Cloud Functions Lead |
| `frontend.agent.md` | `@FrontendLead` | Next.js / Frontend Lead |
| `content.agent.md` | `@ContentLead` | Hugo / Content Lead |
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
