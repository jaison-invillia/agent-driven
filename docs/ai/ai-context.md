# 🤖 AI Context

This document provides **structured context for AI assistants** (GitHub Copilot, coding agents, LLMs) working in this repository.

The goal is to reduce hallucinations, improve code generation, and ensure changes respect the defined architecture and domain.

---

# 🎯 Objective

Ensure that any AI generating code in this project:

- understands the **system's purpose**
- respects **architecture and domain**
- uses **the correct APIs and database**
- avoids inventing entities, endpoints, or rules

---

# 📚 Mandatory context documents

Before generating code, the AI must read:

1. `README.md`
2. `docs/architecture.md`
3. `docs/domain.md`
4. `docs/database.md`
5. `docs/api-spec.md`
6. `docs/engineer-guidelines.md`

These documents are the **official source of truth for the project**.

If any information is missing, the AI must **suggest a documentation change before generating code**.

---

# 🧭 About the system

<!-- [FILL] Describe the system at a high level. -->

> Update this section with the project's purpose and main flow.
> See `docs/domain.md` for the domain model and `CONTEXT_PACK.md` for the condensed snapshot.

---

# 🧱 Architecture

<!-- The architectural style is defined in docs/architecture.md. Adapt per the project. -->

The adopted architectural style is documented in:

➡ `docs/architecture.md`

AI **must respect the style and dependency rules defined** in that document.

---

# 📊 System entities

<!-- [FILL] List the project's domain entities. -->

> Update with the entities defined in `docs/domain.md`.

AI **must not create new entities without updating the domain**.

---

# 🗄 Database

<!-- [FILL] Specify the database used and main tables. -->

> Update with information from `docs/database.md`.

AI must not:

- invent columns
- alter schema without a migration
- ignore constraints

---

# 🌐 API

The API follows the REST pattern.

Base path:

/api/v1

<!-- [FILL] List the main API resources. -->

> Update with the resources defined in `docs/api-spec.md`.

AI must generate controllers and routes **conforming to the API_SPEC**.

---

# 🧪 Tests

Recommended strategy:

- unit tests → domain and use cases
- integration tests → repositories and API
- e2e → main flow

Use cases should be tested with **port mocks**, not with a real database.

---

# ⚠️ Important rules for AI

AI **MUST NOT**:

- create undocumented endpoints
- create undefined tables
- put business logic in controllers
- access the database directly in controllers
- mix architecture layers

AI **MUST**:

- follow the architecture defined in `docs/architecture.md`
- generate small, incremental code
- suggest documentation changes when needed

---

# 🧠 Code generation strategy

When implementing a feature, AI should follow this order:

1️⃣ Verify the domain supports the feature  
2️⃣ Verify the API already defines the endpoint  
3️⃣ Create/adjust Use Case in the Application layer  
4️⃣ Implement adapter (repository/service) in Infrastructure  
5️⃣ Create controller in the Interfaces layer  
6️⃣ Add tests  

---

# 📌 Conventions

<!-- [FILL] Define the project stack. Use /setup-project to configure. -->

- backend language: [FILL] (e.g.: Node.js, Python, Java, Go)
- frontend: [FILL] (e.g.: Next.js, Nuxt.js, SvelteKit, Angular)
- database: [FILL] (e.g.: PostgreSQL, MySQL, MongoDB)

Prefer:

- explicit code
- small functions
- single responsibility

---

# 🚫 Anti-patterns AI should avoid

- controllers with business logic
- scattered SQL queries
- circular dependencies
- violation of architectural boundaries defined in `docs/architecture.md`
- generation of unused code

---

# 🧩 Evolution

If AI identifies:

- architectural inconsistency
- need for a new entity
- domain change
- new endpoint

It must:

1. Propose a documentation change
2. Only then generate code

---

# 🤖 Agent Squad

This repository uses **14 specialized AI agents** that collaborate in agile flows.

Main agents:
- `pathfinder` — flow advisor for uncertain tasks (optional entry point)
- `product-owner` — demands → tracker drafts/cards
- `architect` — architectural analysis
- `staff` — central orchestrator
- `dba` — database analysis (schema, migrations, constraints, indexes, rollback)
- `devops` — CI/CD, infrastructure, containers, deployment, environment configuration
- `backend-dev` / `frontend-dev` — implementation (Staff sub-agents)
- `test-advisor` — testing strategy by classification (`feature_nova`/`mudanca_existente`)
- `qa` — validation and test execution
- `reviewer` — PR code review when code changes exist
- `documenter` — documentation mini-plan at start + final update
- `metrifier` — metrics and observability
- `project-setup` — initial project stack configuration

Available slash commands:
- `/setup-project` — configure project stack and tooling (Project Setup)
- `/plan-task` — diagnose task and suggest flow (Pathfinder)
- `/new-feature` — new feature (PO)
- `/analyze-issue` — architectural analysis (Architect)
- `/analyze-database` — database analysis (DBA)
- `/implement-issue` — plan and implement (Staff)
- `/review-pr` — code review (Reviewer)
- `/fix-bug` — bug fix (Staff)
- `/document-pr` — document changes (Documenter)
- `/devops` — CI/CD, infrastructure, containers, deployment (DevOps)
- `/analyze-infra` — infrastructure/CI impact analysis (DevOps)

Full reference:
- `AGENTS.md`
- `docs/agent-task-flow.md`
- `docs/ai/agent-squad-guide.md`
- `docs/ai/usage-guide.md` — practical guide with usage examples
