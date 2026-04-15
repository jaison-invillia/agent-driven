# 📋 Engineering Documentation — Recommendations

Guide to recommended engineering documents for any software project. Organized by priority level, with objective, when to create, and minimum template for each document.

---

## Fundamental Documents (mandatory)

Documents every project should have from the beginning.

### README.md
- **Objective**: Project overview, how to run, technology stack
- **When to create**: On the first commit
- **Minimum template**:
  - What the project is (1-2 paragraphs)
  - Technology stack
  - How to run locally
  - How to run tests
- **Example in this repo**: `README.md`

### architecture.md
- **Objective**: Architectural style, layers, dependency rules
- **When to create**: Before writing code
- **Minimum template**:
  - Architectural style (Clean Arch, Hexagonal, MVC, etc.)
  - Layer diagram
  - Dependency rules between layers
  - What each layer does and what is forbidden
- **Example in this repo**: `docs/architecture.md`

### domain.md
- **Objective**: Domain entities, business rules, glossary
- **When to create**: During domain modeling
- **Minimum template**:
  - List of entities with attributes
  - Business rules per entity
  - Domain terms glossary
  - Main system flow
- **Example in this repo**: `docs/domain.md`

### database.md
- **Objective**: Database schema, constraints, indexes, migrations
- **When to create**: When the database is defined
- **Minimum template**:
  - Table diagram (or description)
  - Fields, types, and constraints per table
  - Foreign keys and cascade rules
  - Relevant indexes
  - Uniqueness rules
- **Example in this repo**: `docs/database.md`

### api-spec.md
- **Objective**: HTTP contracts, authentication, error formats
- **When to create**: Before implementing endpoints
- **Minimum template**:
  - Base path and versioning
  - Authentication scheme
  - List of endpoints (method, path, request, response)
  - Standard error format
  - Error codes
- **Example in this repo**: `docs/api-spec.md`

### security.md
- **Objective**: Security baseline, authentication, validation
- **When to create**: Before the first endpoint
- **Minimum template**:
  - Authentication method
  - Input validation rules
  - Sensitive data (what to never log)
  - Injection prevention
  - Access control
- **Example in this repo**: `docs/security.md`

### engineer-guidelines.md
- **Objective**: Code standards, naming, tests, Definition of Done
- **When to create**: Before the first PR
- **Minimum template**:
  - Naming conventions (files, classes, functions)
  - Test pyramid (unit > integration > e2e)
  - Commit/branch rules
  - Definition of Done (checklist)
- **Example in this repo**: `docs/engineer-guidelines.md`

---

## Support Documents (recommended)

Documents that increase team quality and productivity.

### observability.md
- **Objective**: Logging, metrics, tracing, alerts
- **When to create**: When configuring logging/APM
- **Minimum template**:
  - Structured log standard (required fields)
  - Request correlation (requestId)
  - APM tool
  - Health endpoints
- **Example in this repo**: `docs/observability.md`

### project-structure.md
- **Objective**: Folder organization and layout conventions
- **When to create**: When the folder structure is defined
- **Minimum template**:
  - Directory tree with explanation
  - Where each type of artifact lives
  - Examples of how to add new artifacts
- **Example in this repo**: `docs/project-structure.md`

### local-setup.md
- **Objective**: How to run the project locally
- **When to create**: When setup has more than 3 steps
- **Minimum template**:
  - Prerequisites (versions, tools)
  - Installation steps
  - Environment variables
  - How to run tests
  - Common troubleshooting
- **Example in this repo**: `docs/local-setup.md`

### ADRs (Architectural Decision Records)
- **Objective**: Record architectural decisions with context and consequences
- **When to create**: At each significant architectural decision
- **Minimum template**:
  - Status (Proposed, Accepted, Deprecated, Superseded)
  - Context (why the decision was needed)
  - Decision (what was decided)
  - Consequences (positive and negative)
- **Example in this repo**: `docs/adr/0000-adr-template.md` (template with instructions and mini-example)
- **Convention**: Sequential numbering `NNNN-slug.md` in `docs/adr/`
- **Full process**: see ADRs section in `docs/engineer-guidelines.md`

---

## AI and Automation Documents

For projects that use AI agents as development assistants.

### ai-context.md
- **Objective**: Condensed context for AI assistants
- **When to create**: When adopting AI agents
- **Minimum template**:
  - Mandatory docs to read
  - System overview
  - Technology stack
  - Anti-patterns for AI to avoid
- **Example in this repo**: `docs/ai/ai-context.md`

### agent-task-flow.md
- **Objective**: Workflow between agents
- **When to create**: When configuring the agent squad
- **Minimum template**:
  - List of agents with responsibilities
  - Delegation diagram
  - Main flows (feature, bug, bootstrap)
  - Golden rules
- **Example in this repo**: `docs/agent-task-flow.md`

### agent-squad-guide.md
- **Objective**: Complete agent squad methodology
- **When to create**: When the squad is operational
- **Content**: File structure, customization types, how to create agents, how to replicate
- **Example in this repo**: `docs/ai/agent-squad-guide.md`

### AGENTS.md (root)
- **Objective**: Agent role and responsibility definitions
- **When to create**: Together with the agents
- **Content**: Agent table, delegation model, execution flows
- **Example in this repo**: `AGENTS.md`

### CONTEXT_PACK.md (root)
- **Objective**: Condensed snapshot for quick AI onboarding
- **When to create**: When documentation grows beyond 5 files
- **Content**: Domain summary, stack, architecture, main rules
- **Example in this repo**: `CONTEXT_PACK.md`

---

## Optional Documents (as maturity grows)

For more mature projects or those with specific needs.

| Document | Objective | When to create |
|----------|----------|---------------|
| `runbook.md` | Operational procedures and troubleshooting | When in production |
| `contributing.md` | Guide for external contributors | Open-source projects or large teams |
| `changelog.md` | Change history by version | When there are releases |
| `testing-strategy.md` | Detailed testing strategy | When the pyramid is complex |
| `deployment.md` | Pipeline, environments, rollback | When there is CI/CD |
| `performance.md` | Benchmarks, SLAs, optimizations | When performance is critical |
| `data-dictionary.md` | Complete data dictionary | Data-intensive projects |
| `integration-guide.md` | How to integrate with the system | When there are external consumers |

---

## General tips

1. **Start with the minimum** — README + architecture + domain are enough to start
2. **Write before coding** — Documenting the decision before implementing saves rework
3. **Keep it alive** — Outdated docs are worse than no docs
4. **Automate verification** — AI agents can validate if implementation follows docs
5. **One doc per concern** — Separating into specific files eases maintenance and reference
6. **Use the Documenter** — The `documenter` agent automatically updates docs after each PR
