# 🧭 Engineering Guidelines

This document defines engineering standards and rules for the project.

References:
- Overview: `README.md`
- Architecture: `docs/architecture.md`
- Domain: `docs/domain.md`
- Database: `docs/database.md`
- API: `docs/api-spec.md`

---

## ✅ Goals

- Increase code predictability and quality.
- Reduce rework in PRs (clear standards).
- Facilitate human + AI collaboration (Copilot/agents).
- Keep the system testable and evolvable.

---

## 🏗️ Architecture

Architectural decisions (style, layers, and dependency rules) are defined in:

➡ `docs/architecture.md`

**Rule:** all contributions must respect the boundaries and responsibilities described in that document.

---

## 📝 ADRs (Architecture Decision Records)

ADRs record significant architectural decisions for the project. Each ADR is a short document with context, decision, consequences, and mitigations.

### When to create an ADR?
- Choice of architectural style, database, framework, or language
- Authentication/authorization pattern
- Logging/observability strategy
- Any technical decision that affects multiple components or is hard to reverse

### Conventions
- **Directory:** `docs/adr/`
- **Numbering:** sequential with 4 digits (`0001`, `0002`, ...)
- **Filename:** `NNNN-descriptive-title.md` in kebab-case
- **Template:** copy `docs/adr/0000-adr-template.md` as a base
- **Lifecycle:** `Proposed` → `Accepted` → `Deprecated` → `Superseded by ADR-NNNN`
- **Approval:** every ADR must be submitted via PR for team review

### Process
1. Copy `docs/adr/0000-adr-template.md` as `docs/adr/NNNN-title.md`
2. Fill in all sections (context, alternatives, decision, consequences)
3. Open a PR for review
4. After approval, change status to `Accepted`

> See `docs/adr/0000-adr-template.md` for the full template with instructions and mini-example.

---

## 🧾 Code Standards

### Language and standard

<!-- [FILL] Define the project stack. Use /setup-project to configure. -->

- Backend: [FILL] (e.g.: Node.js + TypeScript, Python, Java, Go)
- Frontend: [FILL] (e.g.: Next.js, Nuxt.js, SvelteKit, Angular)
- Prefer **explicit code** over "magic" (especially with AI).

### Naming
- Folders: `kebab-case` (e.g.: `order-items`)
- Files: `kebab-case` (e.g.: `order-service.[ext]`)
- Classes: `PascalCase`
- Functions/variables: `camelCase`
- Constants: `UPPER_SNAKE_CASE`

### DTOs
- HTTP requests/responses must use **DTOs**, not domain entities directly.
- HTTP DTOs live in `interfaces/http/dto`.
- Do not "leak" infrastructure details (e.g.: database fields) into public DTOs.

---

## 🧪 Tests

### Recommended pyramid
- **Unit tests (main focus):** domain + application
- **Integration tests:** repositories + critical API endpoints
- **E2E (minimum viable):** main happy path

### Rules
- Each Use Case must have tests for:
  - happy path
  - input validation
  - expected error cases (e.g.: resource not found)
- Use Cases must be tested with **port mocks** (interfaces), not with a real database.

---

## 🔐 Security

- JWT required for protected routes (see `docs/api-spec.md`).
- Never log:
  - passwords
  - full tokens
  - sensitive data
- Validate input on all endpoints.
- Protect against SQL injection (parameterized queries / ORM).
- Rate limiting (future) for auth endpoints.

---

## 📦 Persistence

- Versioned migrations in the project's migrations directory (per `docs/project-structure.md`).
- Database constraints **should reflect domain rules** when possible.
- Uniqueness as defined in `docs/database.md`.

---

## 🧰 Observability & Logs

### APM / Monitoring

<!-- [FILL] Define the APM tool (e.g.: Datadog, New Relic, Grafana, CloudWatch). -->

- Instrument API and capture unhandled errors.
- Ensure correlation via `requestId` (see error pattern in `docs/api-spec.md`).

### Logging
Minimum requirements for the logger:
- levels: `debug`, `info`, `warn`, `error`
- structured logs (JSON)
- include `requestId` whenever possible
- do not include sensitive data

---

## 🔁 Git Workflow

- Branches:
  - `main`: stable
  - `feat/<topic>`: features
  - `fix/<topic>`: fixes
  - `chore/<topic>`: maintenance
- PR required for merge into `main`.
- PR must include:
  - description of what changed
  - test checklist
  - issue link (if any)

---

## ✅ Definition of Done (DoD)

A task is only "done" when:
- [ ] code implemented respecting `docs/architecture.md`
- [ ] tests (unit/integration) added when applicable
- [ ] lint/format passing
- [ ] endpoints adhering to `docs/api-spec.md`
- [ ] migrations included when schema changed
- [ ] adequate logs and error handling
- [ ] documentation updated (if necessary)

---

## 🤖 Rules for AI (Copilot / Agents)

- Always read before coding:
  - `README.md`
  - `docs/architecture.md`
  - `docs/domain.md`
  - `docs/database.md`
  - `docs/api-spec.md`
  - this document `docs/engineer-guidelines.md`
- Do not invent endpoints or tables outside the documentation.
- When in doubt: propose a documentation change **before** generating code.
- Avoid "embellishing" code (emojis, redundant comments, etc.).
- Prefer small changes with smaller, reviewable commits/PRs.
