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
2. Type `@AgentName` in GitHub Copilot Chat to invoke any agent.
3. Agents use `codebase` and `search` tools to access code across all repos in the workspace.

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
