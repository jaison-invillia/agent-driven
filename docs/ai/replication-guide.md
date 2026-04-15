# 🔄 Replication Guide

Step-by-step guide to create a new project from this template.

> **Note**: This repository IS the template. Use it as a base to create new projects.

---

## Prerequisites

- VS Code with the GitHub Copilot extension (with agent support)
- Access to the GitHub MCP (pre-configured in Copilot)
- Git repository initialized from this template

---

## Step 1 — Create a repository from the template

Use this repository as a template on GitHub ("Use this template" button) or copy the structure manually.

The inherited structure:

```
.github/
  agents/            # 14 specialized agents
  prompts/           # 11 slash commands
  instructions/      # 8 contextual instructions
  skills/            # 2 skills (issue-triage, full-feature-cycle)
  copilot-instructions.md

docs/                # Structured documentation with placeholders
AGENTS.md            # Squad definition
CONTEXT_PACK.md      # Condensed snapshot with placeholders
README.md            # Template guide
```

---

## Step 2 — Configure MCP

### GitHub MCP (required)
The GitHub MCP comes pre-configured with Copilot. Check `.vscode/mcp.json`:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

### Additional MCPs (optional)
To add Figma, Playwright, or other MCPs, add entries to `mcp.json`:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "playwright": {
      "type": "http",
      "url": "<playwright-mcp-url>"
    }
  }
}
```

---

## Step 3 — Configure the project stack (recommended)

Use the `/setup-project` command in Copilot Chat. The `project-setup` agent will ask about the project stack and automatically update all documentation.

```
/setup-project
```

Alternatively, manually fill in the `[FILL]` placeholders.

## Step 4 — Fill in domain documentation

### Documents with `[FILL]` placeholders
The following documents have a ready structure — just fill in with your project data:

| Document | Required? | Purpose |
|----------|----------|---------|
| `docs/domain.md` | ✅ | Entities and business rules |
| `docs/api-spec.md` | ✅ (if has API) | HTTP contracts |
| `docs/database.md` | ✅ (if has DB) | Schema and constraints |
| `docs/local-setup.md` | ✅ | Local environment setup |
| `CONTEXT_PACK.md` | Recommended | Snapshot for quick onboarding |
| `docs/ai/ai-context.md` | Recommended | Condensed context for AI |

### Pre-filled documents (review and adapt if needed)

| Document | Purpose |
|----------|---------|
| `docs/architecture.md` | Architectural style and layers |
| `docs/security.md` | Security baseline |
| `docs/engineer-guidelines.md` | Code standards and tests |
| `docs/observability.md` | Logging and monitoring |
| `docs/project-structure.md` | Folder layout |

## Step 5 — Customize the agents (if needed)

### Minimum adjustments needed

> **Note:** If you used `/setup-project`, most of these adjustments were already made automatically.

1. **Product Owner**: Adapt if using Jira instead of GitHub Issues
2. **Architect**: Adapt ADR references and architectural style
3. **Backend/Frontend**: Adapt for the project's technology stack
4. **Test Advisor**: Adapt for the project's testing framework

### Instruction adjustments

Adapt the `applyTo` patterns in `.instructions.md` files for the new project's folder structure.

Review each `.instructions.md` and adjust:
- `applyTo` patterns for the new project structure
- Naming conventions if different
- Stack-specific rules

---

## Step 6 — Test

### Quick test of each agent
1. Open VS Code in the new repository
2. In Copilot Chat, invoke each agent and verify it responds according to its role
3. Test each slash command (`/new-feature`, `/analyze-issue`, etc.)

### End-to-end flow test
1. `/new-feature` → PO should create an issue
2. `/analyze-issue #N` → Architect should post analysis
3. `/implement-issue #N` → Staff should plan and delegate
4. `/review-pr #N` → Reviewer should review
5. `/document-pr #N` → Documenter should update docs

---

## Step 7 — Document

Create or update the `AGENTS.md` of the new repository with:
- List of active agents
- Project-specific workflows
- Customizations made during replication

---

## Replication checklist

- [ ] Repository created from template
- [ ] MCP configured in `.vscode/mcp.json`
- [ ] `/setup-project` executed (or placeholders manually filled)
- [ ] `docs/domain.md` filled with entities and business rules
- [ ] `docs/database.md` filled with schema and constraints
- [ ] `docs/api-spec.md` filled with endpoints and contracts
- [ ] `docs/local-setup.md` filled with setup instructions
- [ ] `CONTEXT_PACK.md` filled with project snapshot
- [ ] `docs/ai/ai-context.md` updated with system context
- [ ] ADRs created for project decisions (`docs/adr/`)
- [ ] Instructions adapted for project structure (if needed)
- [ ] Each agent tested
- [ ] End-to-end flow tested

---

## Tips

- **Start with `/setup-project`**: Automated configuration saves time and ensures consistency
- **Start simple**: Use only PO + Architect + Staff + BE/FE + Reviewer at the beginning
- **Add gradually**: QA, Documenter, and Metrifier can be added later
- **Document first**: Agent quality depends on documentation quality
- **Iterate**: Adjust agent instructions as you learn what works best
