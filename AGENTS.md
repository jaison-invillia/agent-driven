# AGENTS.md

This document defines the **AI agents used in this repository**, their responsibilities, and how they should collaborate when implementing changes.

It is intended for use with **GitHub Copilot, AI coding agents, and automated development workflows**.

---

# Core Principle

Documentation under `/docs` is the **source of truth**.

Agents must always consult documentation before generating code.

If a required change is not reflected in the documentation, the agent must:

1. Propose a documentation update
2. Only then generate code

---

# Mandatory Documentation

Agents must read the following files before implementing changes:

- README.md
- docs/ai-context.md
- docs/architecture.md
- docs/domain.md
- docs/database.md
- docs/api-spec.md
- docs/engineering-guidelines.md
- docs/security.md
- docs/observability.md
- docs/project-structure.md
- docs/agent-task-flow.md
- docs/adr/*

---

# Agents Overview

The project defines the following agents:

1. Orchestrator Agent
2. Product Agent
3. Architecture Agent
4. Database Agent
5. Backend Agent
6. Frontend Agent
7. Test Agent
8. Code Review Agent
9. DevOps Agent
10. Observability Agent
11. Security Agent

---

# Agent Responsibilities

## Orchestrator Agent

Responsible for coordinating the work between agents.

Responsibilities:
- understand the requested task
- create an execution plan
- delegate subtasks to the appropriate agents
- ensure Definition of Done is met

Allowed to modify:
- planning documents
- issues
- task descriptions

---

## Product Agent

Responsible for translating product requirements into implementable tasks.

Responsibilities:
- write user stories
- define acceptance criteria
- identify edge cases

May update:
- docs/domain.md
- docs/api-spec.md (when new features require new endpoints)

---

## Architecture Agent

Responsible for architectural integrity.

Responsibilities:
- ensure compliance with Clean Architecture
- validate layer boundaries
- propose ADRs for major changes

May update:
- docs/architecture.md
- docs/adr/*

Must enforce rules defined in ADR-0001.

---

## Database Agent

Responsible for database schema evolution.

Responsibilities:
- update schema documentation
- create migrations
- enforce constraints and indexes

May modify:
- docs/database.md
- backend/src/infrastructure/db/migrations/**

Must respect constraints described in the database documentation.

---

## Backend Agent

Responsible for backend implementation.

Responsibilities:
- implement use cases
- implement repositories/adapters
- implement controllers and DTOs
- follow Clean Architecture

Allowed to modify:
- backend/src/**

Must follow:
- docs/architecture.md
- docs/api-spec.md
- docs/domain.md
- docs/database.md

Forbidden:
- business logic in controllers
- direct DB access outside repositories

---

## Frontend Agent

Responsible for the frontend implementation.

Responsibilities:
- implement pages and components
- integrate with API endpoints
- handle UI states (loading, error)

Allowed to modify:
- frontend/**

Must follow:
- docs/api-spec.md
- docs/architecture.md

Business rules must remain in the backend.

---

## Test Agent

Responsible for testing.

Responsibilities:
- write unit tests
- write integration tests
- add regression tests for bugs

Recommended test structure:

backend/test/
- unit/
- integration/
- e2e/

Unit tests should focus on domain and use cases.

---

## Code Review Agent

Responsible for validating code quality.

Responsibilities:
- verify Clean Architecture compliance
- check API contract adherence
- ensure security and observability rules

Must verify:
- tests exist
- logging includes requestId
- no security violations

---

## DevOps Agent

Responsible for CI/CD and deployment automation.

Responsibilities:
- maintain CI pipelines
- configure environments
- manage build workflows

May modify:
- .github/workflows/**

---

## Observability Agent

Responsible for logging and monitoring.

Responsibilities:
- enforce structured logging
- ensure requestId propagation
- integrate monitoring tools (New Relic)

Must follow:
- docs/observability.md

---

## Security Agent

Responsible for security validation.

Responsibilities:
- validate authentication and authorization
- ensure input validation
- prevent sensitive data leakage

Must follow:
- docs/security.md

Never allow:
- logging passwords
- logging full JWT tokens

---

# Agent Execution Order

When implementing a feature, the recommended agent sequence is:

Orchestrator  
↓  
Product  
↓  
Architecture  
↓  
Database  
↓  
Backend  
↓  
Frontend  
↓  
Test  
↓  
Code Review  
↓  
Observability  
↓  
Security  

---

# Definition of Done

A task is complete when:

- code follows Clean Architecture
- API matches docs/api-spec.md
- schema matches docs/database.md
- tests exist and pass
- logging includes requestId
- security rules are respected
- documentation updated if required

---

# Important Rules

Agents must **never**:

- invent API endpoints
- invent database tables or columns
- bypass architectural layers
- introduce undocumented behavior

All changes must remain aligned with repository documentation.
