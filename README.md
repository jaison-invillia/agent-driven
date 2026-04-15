# Copilot Agent Template

Template for project development with a **squad of 14 specialized AI agents**, using GitHub Copilot.

Includes: configured agents, slash commands, skills, contextual instructions, structured documentation, and automated flows (feature, bug fix, review, documentation).

## Project Stack

<!-- [FILL] Define your project stack here or use the /setup-project command to configure automatically. -->

- **Backend:** [FILL] (e.g.: Node.js, Python, Java, Go, .NET)
- **Frontend:** [FILL] (e.g.: Next.js, Nuxt.js, SvelteKit, Angular)
- **Database:** [FILL] (e.g.: PostgreSQL, MySQL, MongoDB, SQLite)
- **Observability:** [FILL] (e.g.: Datadog, New Relic, Grafana, CloudWatch)

> Use the `project-setup` agent (`/setup-project` command) to configure the stack and automatically update all docs and instructions.

## How to Use This Template

### 1. Create a repository from the template

Use this repository as a template to create a new project.

### 2. Configure the project stack (recommended)

Use the `/setup-project` command in Copilot Chat to interactively configure the project stack. The `project-setup` agent will collect information and automatically update all documentation.

Alternatively, manually fill in the `[FILL]` placeholders in the docs.

### 3. Fill in domain documentation

The docs below contain `[FILL]` placeholders — fill in with your project data:

| Document | What to fill |
|----------|-------------|
| [docs/domain.md](docs/domain.md) | Entities, business rules, relationships, main flow |
| [docs/database.md](docs/database.md) | Tables, fields, constraints, indexes, SQL schema |
| [docs/api-spec.md](docs/api-spec.md) | Endpoints, request/response, authentication, error format |
| [docs/local-setup.md](docs/local-setup.md) | Database, environment variables, setup steps |
| [CONTEXT_PACK.md](CONTEXT_PACK.md) | Condensed project snapshot for quick AI onboarding |

### 4. Review engineering documentation

These docs are pre-filled with generic standards — review and adapt as needed:

| Document | Content |
|----------|---------|
| [docs/architecture.md](docs/architecture.md) | Architectural style and dependency rules |
| [docs/security.md](docs/security.md) | Security baseline (auth, validation, OWASP) |
| [docs/observability.md](docs/observability.md) | Logging, requestId, metrics, alerts |
| [docs/engineer-guidelines.md](docs/engineer-guidelines.md) | Naming, tests, git workflow, DoD |
| [docs/project-structure.md](docs/project-structure.md) | Recommended folder structure |

### 5. Configure MCP

Configure `.vscode/mcp.json` with the MCP for the tracker/tools defined in the project. The template comes with GitHub by default, but the flow can be adapted for Jira, Linear, or another tracker.

Minimal GitHub example:

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

Optional MCPs: Playwright (e2e tests), DB, Figma, and an alternative tracker.

> If there is no MCP with write permission for the chosen tracker, the `product-owner` operates in `draft-only` mode: shows the card draft and waits for manual creation.

### 6. Create project ADRs

The template includes:
- `docs/adr/0000-adr-template.md` — Standard template with instructions and mini-example (copy to create new ADRs)

Create ADRs for project decisions (architectural style, language, database, framework, auth, hosting, etc.).

See `docs/engineer-guidelines.md` (ADRs section) for the full process.

### 7. Customize instructions (optional)

Review and customize according to the project stack:

| File | Purpose |
|------|---------|
| `.github/copilot-instructions.md` | General Copilot instructions |
| `.github/instructions/backend-architecture.instructions.md` | Layer boundaries and naming |
| `.github/instructions/database-migrations.instructions.md` | Migration patterns |
| `.github/instructions/testing.instructions.md` | Test pyramid and mocking |
| `.github/instructions/api-controllers.instructions.md` | Response format and validation |
| `.github/instructions/security.instructions.md` | Auth, JWT, validation |
| `.github/instructions/frontend-pages.instructions.md` | Frontend framework patterns |
| `.github/instructions/devops-infra.instructions.md` | CI/CD, Docker, Terraform, deployment |
| `.github/instructions/issue-tracking.instructions.md` | Tracker card management |

## Agents

14 specialized agents with defined roles:

| Agent | Role |
|-------|------|
| **Product Owner** | Demands → drafts and tracker cards with acceptance criteria |
| **Architect** | Architectural analysis of issues |
| **Staff** | Orchestrator: plans, delegates, opens PR |
| **DBA** | Database analysis: schema, migrations, constraints, indexes, rollback |
| **Backend Dev** | Backend implementation (sub-agent) |
| **Frontend Dev** | Frontend implementation (sub-agent) |
| **Test Advisor** | Proposes testing strategy by classification |
| **QA** | Executes tests and validates criteria |
| **Reviewer** | Code review against guidelines (when code changes exist) |
| **Documenter** | Documentation mini-plan at start + final update |
| **Metrifier** | Recommends metrics and observability |
| **Project Setup** | Configures stack and updates template docs |
| **Pathfinder** | Diagnoses uncertain tasks and suggests agent workflow |
| **DevOps** | CI/CD pipelines, infrastructure, containers, deployment |

Full details: [AGENTS.md](AGENTS.md)

## Slash Commands

| Command | Agent | What it does |
|---------|-------|-------------|
| `/setup-project` | Project Setup | Configures stack and updates docs |
| `/plan-task` | Pathfinder | Diagnoses task and suggests agent workflow |
| `/new-feature` | Product Owner | Drafts card and creates after approval |
| `/analyze-issue` | Architect | Architectural analysis |
| `/analyze-database` | DBA | Database impact analysis |
| `/implement-issue` | Staff | Plans and implements |
| `/fix-bug` | Staff | Investigates and fixes bug |
| `/review-pr` | Reviewer | Reviews PR |
| `/document-pr` | Documenter | Updates docs |
| `/devops` | DevOps | CI/CD, infrastructure, deployment |
| `/analyze-infra` | DevOps | Infrastructure/CI impact analysis |

## References

- Agent usage guide: [docs/ai/usage-guide.md](docs/ai/usage-guide.md)
- Squad guide: [docs/ai/agent-squad-guide.md](docs/ai/agent-squad-guide.md)
- Agent flow: [docs/agent-task-flow.md](docs/agent-task-flow.md)
- AI context: [docs/ai/ai-context.md](docs/ai/ai-context.md)
- Docs recommendations: [docs/engineering-docs-recommendation.md](docs/engineering-docs-recommendation.md)

## License

To be defined.
