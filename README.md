# Agent Organization

This repo contains the GitHub Copilot custom agent definitions for the project ecosystem.

## What are agents?

Agents are `.agent.md` files that give [GitHub Copilot Chat](https://docs.github.com/en/copilot) persistent personas with scoped responsibilities. When you `@mention` an agent in chat, Copilot adopts that persona and follows its instructions.

## Structure

All agents live in `.github/agents/` — the only path GitHub Copilot discovers.

```
.github/agents/
├── ghabs.agent.md          # CEO & Founder
├── atlas.agent.md          # CTO
├── architect.agent.md      # Principal Architect
├── business.agent.md       # Strategic Advisor
├── privacy.agent.md        # CARO / DPO
├── ops.agent.md            # SRE / DevOps
├── scribe.agent.md         # Documentation
├── web-director.agent.md   # Web Experience
├── product-designer.agent.md # Product Design
├── project-lead.agent.md   # Orchestrator
├── growth-lead.agent.md    # Growth Engineering
├── mobile-lead.agent.md    # Mobile App
├── backend.agent.md        # Backend API
├── cloud-arch.agent.md     # Infrastructure
├── quant-architect.agent.md # Domain Specialist
├── frontend.agent.md       # Web App
├── content.agent.md        # Landing / Marketing
└── qa-guard.agent.md       # Quality Assurance
```

## Usage

1. Open this repo as part of a **VS Code multi-root workspace** alongside your project repos.
2. **GitHub Copilot:** Type `@AgentName` in chat to invoke any agent using `.github/agents/`.
3. **Antigravity:** Use the `run_workflow` tool or `@mention` a skill (e.g., `@mobile_lead`) using the `.agent/` directory.

## Antigravity

This repository is optimized for **Antigravity**, featuring executable workflows and specialized skills.

- **Skills:** Found in `.agent/skills/`. Each skill encapsulates an agent's persona and logic.
- **Workflows:** Found in `.agent/workflows/`. These are individual, executable SOPs for features, bugs, releases, and more.
- **Rules:** Found in `.agent/rules/`. These enforce the organizational "Constitution" (see `bm-org.md` or `wlbl-org.md`).

## Customization

See [AGENT-ORG-PLAYBOOK.md](AGENT-ORG-PLAYBOOK.md) for the full blueprint — org structure, design principles, governance mechanisms, and step-by-step instructions to adapt this organization for a new project.

## File format

```markdown
---
name: AgentName
description: 'One-line role description.'
agents: ['DelegateA', 'DelegateB']
tools: ['codebase', 'search', 'terminal', 'agent']
---
# Agent Title
Persona definition, responsibilities, collaboration rules...
```

| Field | Description |
|---|---|
| `name` | Unique PascalCase identifier — used as `@Name` in chat |
| `description` | One-line summary shown in Copilot's agent picker |
| `agents` | Agents this one can delegate to (`[]` if none) |
| `tools` | Copilot tools: `codebase`, `search`, `terminal`, `edit`, `agent` |
