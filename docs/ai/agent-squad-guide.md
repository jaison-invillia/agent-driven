# 📚 AI Agent Squad Guide

This document describes the **AI agent squad methodology** used in this repository, how it works, how to create new agents, and how to customize for different contexts.

---

## Overview

The squad is composed of **14 specialized agents** that collaborate following a flow inspired by agile models. Each agent has a defined role, restricted tools, and a clear scope of action.

Communication between agents happens in two ways:
1. **Via configured tracker**: Agents read/write to cards/issues/tickets when the project MCP supports it
2. **Via direct delegation (only by `staff`)**: The `staff` (orchestrator) invokes sub-agents automatically

---

## File structure

```
.github/
  agents/                          # Agents (.agent.md)
    product-owner.agent.md         # Converts demands into tracker drafts/cards
    architect.agent.md             # Architectural analysis
    staff.agent.md                 # Orchestrator (central)
    dba.agent.md                   # Database decisions and analysis
    backend-dev.agent.md           # Backend implementation (sub-agent)
    frontend-dev.agent.md          # Frontend implementation (sub-agent)
    test-advisor.agent.md          # Testing strategy
    qa.agent.md                    # Validation and tests
    reviewer.agent.md              # Code review
    documenter.agent.md            # Documentation mini-plan + final update
    metrifier.agent.md             # Metrics and observability
    project-setup.agent.md         # Initial stack configuration
    pathfinder.agent.md            # Flow advisor for uncertain tasks
    devops.agent.md                # CI/CD, infrastructure, deployment

  prompts/                         # Slash commands (.prompt.md)
    setup-project.prompt.md        # /setup-project
    plan-task.prompt.md            # /plan-task
    new-feature.prompt.md          # /new-feature
    analyze-issue.prompt.md        # /analyze-issue
    analyze-database.prompt.md     # /analyze-database
    implement-issue.prompt.md      # /implement-issue
    fix-bug.prompt.md              # /fix-bug
    review-pr.prompt.md            # /review-pr
    document-pr.prompt.md          # /document-pr
    devops.prompt.md               # /devops
    analyze-infra.prompt.md        # /analyze-infra

  instructions/                    # Contextual guidelines (.instructions.md)
    backend-architecture.instructions.md
    api-controllers.instructions.md
    database-migrations.instructions.md
    frontend-pages.instructions.md
    testing.instructions.md
    security.instructions.md
    issue-tracking.instructions.md
    devops-infra.instructions.md

  skills/                          # Complex workflows
    issue-triage/
      SKILL.md                     # Full triage
      references/triage-criteria.md
    full-feature-cycle/
      SKILL.md                     # Full feature cycle
      references/checklist.md

  copilot-instructions.md          # General repo instructions

AGENTS.md                          # Squad definition (root)
```

---

## How each file type works

### `.agent.md` — Agents
Defines a role/persona with restricted tools and behavior instructions.

```yaml
---
name: agent-name              # Unique identifier
description: "Use when: ..."  # Trigger words for discovery
tools: [read, search]         # Allowed tools
user-invocable: true          # Appears in chat picker?
agents: [sub-agent1]          # Allowed sub-agents (optional)
argument-hint: "..."          # Input hint (optional)
---

[Detailed agent instructions]
```

### `.prompt.md` — Slash commands
Template for specific tasks, invoked with `/name` in chat.

```yaml
---
description: "..."
agent: "agent-name"           # Which agent executes
argument-hint: "..."          # Input hint
---

[Prompt instructions]
```

### `.instructions.md` — Contextual guidelines
Rules that apply automatically when matching files are edited.

```yaml
---
description: "Use when: ..."
applyTo: "backend/src/**/*.ts"  # Glob pattern
---

[Rules and guidelines]
```

### `SKILL.md` — Complex workflows
Multi-step procedures with additional resources (scripts, templates, references).

```yaml
---
name: skill-name
description: "..."
---

[Step-by-step procedure]
```

---

## Typical workflow

> **Tip:** When the task is uncertain or you don't know where to start, use `/plan-task` to have the `pathfinder` suggest the ideal flow.

### New feature
```
(pathfinder) → /new-feature → PO shows draft and creates card after approval → /analyze-issue #N → Architect analyzes →
/implement-issue #N → Staff clarifies ambiguities, triggers documenter, classifies tests and delegates →
BE/FE implement → QA validates → /review-pr #PR (only if code changed) → Reviewer reviews →
/document-pr #PR → Documenter documents
```

In the Architect step, when the task is not yet planned for high-level documentation/testing, it should request this in the issue comment (without delegating execution):
- `documenter`: how to document the demand
- `test-advisor`: how to test the demand at a high level

"Already planned" means BOTH:
- relevant subtask in the issue checklist
- prior agent comment requesting/providing the guidance

### Bug fix
```
/fix-bug → Staff investigates and plans → BE/FE fix →
QA validates → /review-pr #PR (only if code changed) → Reviewer reviews → /document-pr #PR
```

---

## How to create a new agent

1. Create a file `.github/agents/<name>.agent.md`
2. Define the YAML frontmatter with `name`, `description`, `tools`
3. Write clear instructions:
   - Role and scope (what it DOES and what it DOES NOT)
   - Mandatory documents to read
   - Execution workflow (step by step)
   - Output format
   - Non-negotiable rules
4. If the agent uses GitHub MCP, add the issue tracking protocol section
5. Update `AGENTS.md` with the new entry
6. If needed, create a corresponding `.prompt.md` prompt

### Validation checklist
- [ ] `description` contains sufficient trigger words
- [ ] `tools` is the minimum necessary
- [ ] Instructions mention mandatory docs
- [ ] Output format is clear and templatable
- [ ] Non-negotiable rules are explicit

---

## Customization for different contexts

### Using Jira instead of GitHub Issues
- Configure a Jira MCP with read/write permission when available
- Adapt the `product-owner`, the `/new-feature` prompt, and the tracking protocol to use "ticket/card" as the standard term
- Without a write MCP, operate in `draft-only` mode and create manually in Jira

### Using Confluence instead of `docs/`
- Adapt `docs/*.md` references to Confluence links
- Consider using a Confluence MCP for automated reading
- Keep a local `CONTEXT_PACK.md` as a cache for the agents

### Adding new MCPs (Figma, Playwright, etc.)
- Configure the MCP in `.vscode/mcp.json`
- Add `<mcp-name>/*` to the `tools` field of relevant agents
- Document the MCP capabilities in the agent instructions

### Different stack (Python, Java, etc.)
- Adapt the instructions files for the new language/framework
- Update `CONTEXT_PACK.md` and `docs/architecture.md`
- Agents are language-agnostic — only the instructions need to change

---

## Fundamental principles

1. **Documentation is the source of truth** — Agents consult docs before acting
2. **Do not invent** — Never create endpoints, tables, or entities not documented
3. **Minimum scope** — Each agent does only what its role defines
4. **Traceability** — Every action is documented in the issue/PR
5. **Clean delegation** — Staff orchestrates, sub-agents execute
6. **Quality by default** — DoD checklist on every task

---

## Next steps

For practical usage examples of each scenario, see `docs/ai/usage-guide.md`.
