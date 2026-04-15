# CONTEXT_PACK.md

This document provides a **condensed context snapshot** of the project so AI assistants
(GitHub Copilot, coding agents, LLMs) can quickly understand the system without scanning
the entire documentation.

Full documentation lives in `/docs`. If something here conflicts with `/docs`, the docs
directory is the **source of truth**.

---

# Project Overview

<!-- [FILL] Fill with your project's name, purpose, and main flow. -->

Project: [Project Name]

Purpose:
[Describe the system's purpose in 1-2 sentences]

Core user flow:

[Step 1] → [Step 2] → [Step 3] → [Result]

---

# Technology Stack

<!-- [FILL] Define the project stack. Use /setup-project to configure automatically. -->

Backend
- [FILL] Language/runtime (e.g.: Node.js, Python, Java, Go, .NET)
- [FILL] Architectural style (e.g.: Clean Architecture, Hexagonal, Modular, MVC)
- [FILL] API type (e.g.: REST, GraphQL, gRPC)

Frontend
- [FILL] Framework (e.g.: Next.js, Nuxt.js, SvelteKit, Angular, none)
- [FILL] Rendering strategy (e.g.: SSR, SSG, SPA, hybrid)

Database
- [FILL] Database (e.g.: PostgreSQL, MySQL, MongoDB, SQLite)

Monitoring
- [FILL] APM/observability tool (e.g.: Datadog, New Relic, Grafana, CloudWatch)

Infrastructure
- [FILL] CI/CD (e.g.: GitHub Actions, GitLab CI, CircleCI)
- [FILL] Containerization (e.g.: Docker, Podman)
- [FILL] Orchestration (e.g.: Kubernetes, ECS, Docker Compose)
- [FILL] IaC (e.g.: Terraform, Pulumi, CloudFormation)
- [FILL] Cloud provider (e.g.: AWS, GCP, Azure, none)

---

# Architectural Style

<!-- [FILL] Adapt according to the architectural style adopted in the project. -->

Backend follows **[FILL] architectural style** (e.g.: Clean Architecture, Hexagonal, Modular, MVC).

Layers:

[FILL] List the layers according to the adopted style.

Dependency direction:

[FILL] Define the dependency direction between layers.

Rules:

- Domain/business logic must not depend on frameworks or infrastructure
- Controllers/handlers must not contain business logic
- Database access must happen through abstractions (repositories/adapters)
- Follow the layer boundaries defined in the architectural style

Reference:
docs/architecture.md

---

# Core Domain Model

<!-- [FILL] List the project's main entities and their relationships. -->

Main entities:

[Entity1]
[Entity2]
[Entity3]

Relationships:

[Entity1] → [relationship] → [Entity2]
[Entity2] → [relationship] → [Entity3]

Reference:
docs/domain.md

---

# Business Logic

<!-- [FILL] Describe the most important business rules. -->

> Document the core domain rules here (e.g.: completeness conditions,
> uniqueness, idempotency, etc.)

Reference:
docs/domain.md

---

# Database Overview

<!-- [FILL] List main tables and constraints. -->

Primary tables:

[table1]
[table2]
[table3]

Important constraints:

[table_x]:
UNIQUE([field1], [field2])

Reference:
docs/database.md

---

# API Overview

Base path:

/api/v1

Authentication:

<!-- [FILL] Define the authentication method (e.g.: JWT Bearer Token) -->

Important endpoints:

<!-- [FILL] List the main endpoints -->

Reference:
docs/api-spec.md

---

# Observability

Logging rules:

- Structured logs
- JSON format
- Include requestId in every request

Error responses must include:

requestId

Monitoring:

[FILL] APM/observability tool

Reference:
docs/observability.md

---

# Security Rules

Never:

- log passwords
- log password hashes
- log full JWT tokens

Always:

- validate input
- protect user scoped resources
- prevent SQL injection

Reference:
docs/security.md

---

# AI / Agent Rules

AI assistants must:

- Read AGENTS.md
- Read docs before implementing features
- Never invent API endpoints
- Never invent database tables
- Respect architectural boundaries defined in `docs/architecture.md`

Preferred workflow:

1. Check domain rules
2. Check API contract
3. Check database schema
4. Implement backend layers
5. Implement frontend integration
6. Add tests
7. Update docs if necessary

---

# Key ADR Decisions

> **[FILL]** Record project ADRs here as decisions are made.
> Use `docs/adr/0000-adr-template.md` as a base to create new ADRs.

---

# AI Agent Squad

This project uses a squad of 14 specialized AI agents.

Agents:

| Agent | Role |
|-------|------|
| product-owner | Converts demands into tracker card drafts and approved work items |
| architect | Architectural analysis |
| staff | Orchestrator — plans and delegates |
| dba | Database decisions: schema, migrations, constraints, indexes, rollback safety |
| backend-dev | Backend implementation (sub-agent) |
| frontend-dev | Frontend implementation (sub-agent) |
| test-advisor | Testing strategy |
| qa | Test execution and validation |
| reviewer | PR code review (only when code changes exist) |
| documenter | Documentation mini-plan at task start + final update |
| metrifier | Metrics and observability |
| project-setup | Initial stack configuration |
| pathfinder | Diagnoses uncertain tasks and suggests agent workflow |
| devops | CI/CD pipelines, infrastructure, containers, deployment strategies, environment configuration |

Delegation model:

staff → [dba(when DB impact), devops(when infra/CI impact), backend-dev, frontend-dev, test-advisor, qa, metrifier, reviewer, documenter]

Main flows:

A) New feature: product-owner → architect → dba(if DB) → devops(if infra/CI) → staff(+documenter-start) → [BE, FE] → qa → reviewer(code-change) → documenter(final)
B) Bug fix: product-owner → dba(if DB) → devops(if infra/CI) → staff(+documenter-start) → [BE/FE] → qa → reviewer(code-change) → documenter(final)
C) Bootstrap: product-owner → architect → project-setup → staff(+documenter-start) → documenter(final)
D) Tech debt: architect → devops(if infra/CI) → staff(+documenter-start) → [BE/FE] → reviewer(code-change) → documenter(final)

Slash commands:

/setup-project, /plan-task, /new-feature, /analyze-issue, /analyze-database, /implement-issue, /review-pr, /fix-bug, /document-pr, /devops, /analyze-infra

Reference:
AGENTS.md
docs/agent-task-flow.md
docs/ai/agent-squad-guide.md

---

# Important Documentation

For deeper context:

docs/architecture.md
docs/domain.md
docs/database.md
docs/api-spec.md
docs/security.md
docs/observability.md
docs/project-structure.md
docs/engineer-guidelines.md
docs/engineering-docs-recommendation.md
