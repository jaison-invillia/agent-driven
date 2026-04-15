# 🏗️ Architecture

This document describes the project's **architectural decisions**.

For an overview see: `README.md`

For domain rules see: `docs/domain.md`

---

## 🧭 Architectural Style

<!-- [FILL] Define the project's architectural style and record the decision in an ADR (docs/adr/). -->
<!-- Style examples: Clean Architecture, Hexagonal, Modular, MVC, CQRS. -->
<!-- Adapt the sections below according to the adopted style. -->

> **[FILL]** Describe the architectural style adopted by the project and the rationale.
>
> Record the decision formally in an ADR: `docs/adr/NNNN-architectural-style.md`

### Layers (define according to the adopted style)

<!-- Example for layered architecture (adapt to the project's style): -->

> **[FILL]** List the project's layers and their responsibilities.
>
> Example (Clean Architecture):
> - **Domain**: pure business entities and rules (no framework)
> - **Application**: use cases and ports (interfaces)
> - **Interfaces**: input adapters (HTTP controllers, DTOs, middlewares)
> - **Infrastructure**: output adapters (DB/repositories, external providers, logger)
> - **Main**: composition/bootstrapping (DI, routes, wiring)
>
> Example (MVC):
> - **Models**: entities and business rules
> - **Controllers**: coordination logic and HTTP
> - **Views/Routes**: presentation and routes

### Dependency rules

<!-- Define the direction of dependency between layers. -->

> **[FILL]** Define the dependency rules between layers.
>
> Example for layered architecture with inward dependency:
> ```
> interfaces      ─┐
> infrastructure  ─┼──> application ───> domain
> main            ─┘
> ```

**General principles (adapt to the adopted style):**

- Inner layers **must not** import outer layers
- Controllers/handlers **must not** contain business logic
- Database access **must** be done through abstractions (repositories/adapters)
- The composition layer (main/bootstrap) is the only one that can reference all others

---

## 🧩 Responsibilities per layer

<!-- [FILL] Detail the responsibilities of each project layer. -->
<!-- The examples below are based on layered architecture — adapt to the adopted style. -->

### Domain / Models layer
- Business entities
- Invariant domain rules
- Value Objects (if applicable)

**Must not contain:** SQL, HTTP, API DTOs, framework dependencies

### Application / Services layer
- Use cases / orchestration logic
- Ports/interfaces for external dependencies
- Internal input/output models (not HTTP)

> **[FILL]** List the project's use cases. Examples:
> - `RegisterUser`
> - `Create[Entity]`
> - `List[Entities]`

### Interfaces / Controllers layer
- HTTP routes and controllers
- Request/response DTOs (aligned with `docs/api-spec.md`)
- Middlewares (auth, requestId, error mapping)

**Rule**: transform HTTP input → use case input and output → HTTP response (no business logic).

### Infrastructure / Adapters layer
- Concrete implementations of abstractions/ports
- DB connection and migrations
- External integrations
- Logger and APM instrumentation

### Composition / Bootstrap layer
- Creation of concrete instances
- Dependency wiring (DI)
- Route definition and server initialization

---

## 📦 Recommended repository structure

### Backend

<!-- [FILL] Adapt the folder structure according to the project's architectural style and language. -->
<!-- The example below is based on layered architecture — adapt to the adopted style. -->

```
backend/
  src/
    [FILL] Organize according to the adopted architectural style.

    # Example for layered architecture:
    domain/               # entities and business rules
      entities/
      errors/

    application/          # use cases and abstractions
      use-cases/
      ports/

    interfaces/           # input adapters (HTTP)
      http/
        controllers/
        routes/
        middlewares/
        dto/
      mappers/

    infrastructure/       # output adapters (DB, services)
      db/
        connection/
        migrations/
      repositories/
      logging/
      providers/

    main/                 # composition and bootstrap
      server/
      container/
```

### Frontend

<!-- [FILL] Adapt according to the project's frontend framework. -->

```
frontend/
  [FILL] Frontend framework structure
  components/
  services/              # API client
  hooks/
  styles/
```

---

## 🔐 Authentication

- Authentication based on **JWT** (Bearer).
- Protected routes must validate tokens via middleware.
- Contracts and examples: `docs/api-spec.md`.

---

## 📈 Observability

<!-- [FILL] Define the project's APM tool (e.g.: Datadog, New Relic, Grafana). -->

- Instrument backend with APM tool (APM + errors).
- Standardize `requestId` for correlation in logs and errors.
- Errors must be mapped to the standard specified in `docs/api-spec.md`.

---

## 🔒 Security

Required:
- input validation on endpoints
- parameterized queries / ORM to prevent SQL injection
- do not log tokens/passwords/sensitive data
- access controls for authenticated routes

---

## 📈 Architectural Evolution

Possible future improvements (when the need arises):
- cache (Redis) for read-intensive operations
- asynchronous processing (queues) for heavy operations
- containerization and horizontal scaling
