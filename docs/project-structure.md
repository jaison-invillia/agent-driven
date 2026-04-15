# 🗂️ Project Structure

This document defines the **recommended repository structure** and code organization conventions for the project.

Goals:
- reduce ambiguity when creating files/folders
- guide human and AI-assisted contributions
- enforce layer boundaries (per `docs/architecture.md`)

References:
- `README.md`
- `docs/architecture.md`
- `docs/engineer-guidelines.md`
- `docs/ai/ai-context.md`
- `docs/agent-task-flow.md`

---

## 📦 Repository structure (top-level)

Recommended:

```
project-root/
  README.md
  AGENTS.md
  CONTEXT_PACK.md

  .github/
    agents/
      product-owner.agent.md
      architect.agent.md
      staff.agent.md
      dba.agent.md
      backend-dev.agent.md
      frontend-dev.agent.md
      test-advisor.agent.md
      qa.agent.md
      reviewer.agent.md
      documenter.agent.md
      metrifier.agent.md
      project-setup.agent.md
      pathfinder.agent.md
      devops.agent.md

    prompts/
      setup-project.prompt.md
      plan-task.prompt.md
      new-feature.prompt.md
      analyze-issue.prompt.md
      analyze-database.prompt.md
      implement-issue.prompt.md
      fix-bug.prompt.md
      review-pr.prompt.md
      document-pr.prompt.md
      devops.prompt.md
      analyze-infra.prompt.md

    instructions/
      backend-architecture.instructions.md
      api-controllers.instructions.md
      database-migrations.instructions.md
      frontend-pages.instructions.md
      testing.instructions.md
      security.instructions.md
      issue-tracking.instructions.md
      devops-infra.instructions.md

    skills/
      issue-triage/
        SKILL.md
      full-feature-cycle/
        SKILL.md

    copilot-instructions.md

  docs/
    architecture.md
    domain.md
    database.md
    api-spec.md
    engineer-guidelines.md
    observability.md
    security.md
    project-structure.md
    agent-task-flow.md
    local-setup.md
    engineering-docs-recommendation.md
    ai/
      ai-context.md
      usage-guide.md
      replication-guide.md
      agent-squad-guide.md
    adr/
      0000-adr-template.md
      [Project ADRs]

  backend/
    package.json
    src/
      ...

  frontend/
    package.json
    pages/
      ...
```

---

## 🧱 Backend

<!-- Adapt the structure according to the architectural style defined in docs/architecture.md -->

### Folder structure (example)

```
backend/
  src/
    domain/
      entities/
      value-objects/          # optional
      errors/

    application/
      use-cases/
      ports/
      dto/                    # optional (app input/output models, not HTTP)

    interfaces/
      http/
        controllers/
        routes/
        middlewares/
        dto/                  # request/response DTOs (HTTP)
      mappers/                # DTO <-> app/domain models

    infrastructure/
      db/
        connection/
        migrations/
      repositories/
      logging/
      observability/
      providers/              # external services (future)

    main/
      container/              # composition root / wiring
      server/                 # server bootstrap
```

> **Note:** This structure is an example based on layered architecture. Adapt according to your project's architectural style and language (see `docs/architecture.md`).

### Quick rules
- **Domain/Models**: zero dependency on framework/DB.
- **Application/Services**: use cases + abstractions; does not import infra/interfaces.
- **Interfaces/Controllers**: HTTP handlers/DTOs; no business logic.
- **Infrastructure**: DB/repos/providers/logging/observability.
- **Composition/Main**: the only layer that "wires everything together".

> Adapt layers and rules according to the project's architectural style (see `docs/architecture.md`).

Full details in: `docs/architecture.md`.

---

## 🧪 Tests (backend)

Recommended organization:

```
backend/
  test/
    unit/
      domain/
      application/
    integration/
      repositories/
      http/
    e2e/                      # minimum viable (happy path)
```

Rules:
- Unit tests focus on `domain` and `application`.
- Integration tests cover repos and critical endpoints.
- E2E covers the system's main flow.

---

## 🌐 Frontend

<!-- [FILL] Adapt the structure according to the frontend framework of the project. -->

### Folder structure (example)

```
frontend/
  [FILL] Frontend framework structure
  
  components/
  hooks/                      # or equivalent
  services/
    api-client.[ext]          # HTTP wrapper
    auth.[ext]                # login/token helpers
    [resource].[ext]          # clients per resource

  styles/
```

### Rules
- API client must reflect `docs/api-spec.md`.
- Do not duplicate domain rules in the frontend (backend is the source of truth).
- UI must handle:
  - loading
  - errors (user-friendly messages)
  - expired token (redirect to login)

---

## 📄 File conventions

### Naming
- Folders and files: `kebab-case` (preferred) **or** framework standard.
- Classes: `PascalCase`
- Functions/variables: `camelCase`
- Constants: `UPPER_SNAKE_CASE`

### Where to put what
- API contracts: `docs/api-spec.md`
- Domain rules: `docs/domain.md`
- Schema: `docs/database.md`
- Observability: `docs/observability.md`
- Security: `docs/security.md`
- Decisions: `docs/adr/*`
- AI context: `docs/ai/ai-context.md`

---

## 🤖 Rules for AI

- Do not create files outside this structure without justification.
- If a new folder is needed:
  - propose a change to `docs/project-structure.md` first.
- Keep changes small and in the correct layers.
