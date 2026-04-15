# 🧩 Agent Task Flow

This document defines **how AI agents should collaborate** throughout the development lifecycle.

Goals:
- Standardize workflow between agents
- Reduce rework and conflicting changes
- Increase predictability and delivery quality
- Ensure adherence to architecture, domain, database, and API contracts

Mandatory references:
- `README.md`
- `docs/ai/ai-context.md`
- `docs/architecture.md`
- `docs/domain.md`
- `docs/database.md`
- `docs/api-spec.md`
- `docs/engineer-guidelines.md`

---

## 👥 Agents and responsibilities

### 1) Product Owner (`product-owner`)
Converts business demands into clear, actionable cards/issues/tickets.

Responsibilities:
- Clarify ambiguous demands (blocking gate)
- Define acceptance criteria (Given/When/Then)
- Assign priority (P0–P3)
- Show the card draft before any tracker write and wait for explicit approval
- Create/update cards in the configured tracker via MCP with subtask checklists
- Operate in `draft-only` mode when there is no write support in the tracker
- Validate implemented solutions against acceptance criteria

Deliverables: Structured card draft and, after approval, card created/updated with context, criteria, priority, and subtasks.

---

### 2) Architect (`architect`)
Analyzes approved cards/issues from an architectural perspective.

Responsibilities:
- Identify affected architectural layers (per `docs/architecture.md`)
- Suggest file/module structure per layer
- Verify compliance with architectural boundaries
- Propose ADRs when necessary
- When not previously planned, request in the card/issue comment:
   - `documenter` guidance on how to document the demand
   - `test-advisor` guidance on high-level testing strategy
- Post architectural analysis as a comment on the card/issue via MCP

Deliverables: Architectural analysis as a comment on the card/issue.

Criteria for "already planned": consider it previously planned only when BOTH are true:
- relevant subtask exists in the card/issue checklist
- prior agent comment requesting/providing this guidance exists

The `architect` does not delegate execution to other agents; it only records the request in the card/issue.

---

### 3) Staff / Orchestrator (`staff`)
Central orchestrator that plans and coordinates implementation.

Responsibilities:
- Read PO context and Architect analysis
- Clarify ambiguities and validate task description quality before starting
- Before starting implementation, confirm that the card/issue already contains BOTH:
   - subtask covering documentation and high-level testing
   - prior agent comment requesting/providing this guidance
- Trigger `documenter` at the start of every task for mandatory documentation mini-plan
- Consult `dba` whenever there is database impact (schema, migrations, constraints, indexes, query performance)
- Consult `devops` whenever there is infrastructure/CI impact (pipelines, Docker, Terraform, deployment)
- Plan implementation at code level (files, order, dependencies)
- Document plan on the card/issue via MCP
- Classify the task as `feature_nova` or `mudanca_existente`
- Consult `test-advisor` for testing strategy by classification
- Delegate to `backend-dev` and/or `frontend-dev` (sub-agents)
- Parallelize delegations whenever dependencies are independent
- Validate results and run tests
- Consult `metrifier` for observability recommendations
- Trigger `reviewer` before finalization only when code changes exist
- Create branch and open PR via MCP

Delegates to: `backend-dev`, `frontend-dev`, `test-advisor`, `qa`, `metrifier`, `reviewer`, `documenter`, `dba`, `devops`

Deliverables: Documented plan on card/issue + opened PR.

---

### 4) Backend Developer (`backend-dev`)
Implements backend code following the architectural style defined in `docs/architecture.md`. **Sub-agent** (invoked by Staff).

Responsibilities:
- Implement domain entities, use cases, ports
- Implement repositories, controllers, DTOs, routes
- Wire dependencies in the composition root
- Write unit and integration tests

---

### 5) Frontend Developer (`frontend-dev`)
Implements frontend code using the framework defined in `CONTEXT_PACK.md`. **Sub-agent** (invoked by Staff).

Responsibilities:
- Implement pages following frontend framework patterns
- Create components, hooks/equivalents, services
- Integrate with API per `docs/api-spec.md`
- Handle UI states (loading, error, empty, expired token)

---

### 6) Test Advisor (`test-advisor`)
Proposes testing strategies without writing code.

Responsibilities:
- Analyze tasks to identify testable scenarios
- Consider `feature_nova` vs `mudanca_existente` classification provided by Staff
- For `feature_nova`, prioritize new tests for new capabilities
- For `mudanca_existente`, prioritize adjusting existing tests when coverage is already sufficient
- Propose tests by pyramid level (unit → integration → e2e)
- Define mocking strategies and fixtures
- Identify edge cases and security scenarios

---

### 7) QA (`qa`)
Validates implementations by executing tests and verifying acceptance criteria.

Responsibilities:
- Execute automated test suites
- Validate acceptance criteria from the issue
- Test edge cases and security scenarios
- Post structured QA report on the issue via MCP

**Future**: Playwright MCP integration for browser-based testing.

---

### 8) Reviewer (`reviewer`)
Reviews Pull Requests against project guidelines.

Responsibilities:
- Review code for compliance with `docs/architecture.md`
- Validate API contract adherence
- Verify security and observability practices
- Verify test existence and coverage
- Post review on the PR via MCP

Trigger rule:
- Required only when code changes exist
- For documentation-only tasks, record review as not applicable on the issue

---

### 9) Documenter (`documenter`)
Assesses and updates documentation from task start through completion.

Responsibilities:
- At the start of every task, produce a mandatory documentation mini-plan (`required`, `optional`, `none`)
- Indicate candidate docs for update and justification
- Analyze PR diff to identify documentable changes
- Update affected docs (api-spec, database, domain, architecture, etc.)
- Create ADRs for architectural decisions
- Update CONTEXT_PACK.md for significant changes

---

### 10) Metrifier (`metrifier`)
Suggests metrics and observability instrumentation.

Responsibilities:
- Propose business, technical, and operational metrics
- Recommend collection methods (logs, APM, custom metrics)
- Suggest alert thresholds and dashboard structure
- Advise on SLI/SLO

---

### 11) Pathfinder (`pathfinder`)
Flow advisor that diagnoses uncertain tasks and suggests the ideal agent roadmap.

Responsibilities:
- Ask diagnostic questions to understand the task's nature, scope, and uncertainty
- Read cards/issues from any configured tracker to gather context
- Map tasks to the appropriate agents and their ideal execution order
- Suggest a high-level development roadmap inspired by agile flows
- Identify risks, dependencies, and ambiguities before execution
- Explain why each agent should (or should not) be involved

Deliverables: Suggested roadmap with agent sequence, justifications, risks, and how to start.

---

### 12) DBA (`dba`)
Database expert for changes involving persistence.

Responsibilities:
- Analyze schema impact for proposed changes
- Define constraint, index, and migration strategy recommendations
- Validate rollback safety (`up`/`down`) and integrity risks
- Keep recommendations engine-agnostic unless explicit rules exist in documentation
- Request documentation updates from `documenter` when database changes occur

Deliverables: Structured database analysis (schema/migration/index/risks) posted on the card/issue.

---

### 13) DevOps (`devops`)
Infrastructure, CI/CD, and deployment expert for this repository.

Responsibilities:
- Analyze issue requirements impacting CI/CD pipelines, infrastructure, or deployment
- Design and create GitHub Actions workflows (or equivalent for the documented stack)
- Create and review Dockerfiles, docker-compose, and container orchestration configurations
- Design and create Infrastructure as Code (Terraform, Helm, or equivalent)
- Propose deployment strategies (blue-green, canary, rolling, feature flags)
- Review pipeline security (secret management, OIDC, least-privilege, SHA pinning)
- Advise on environment configuration and promotion strategies
- Define rollback strategies and health check patterns
- Request documentation updates from `documenter` when infrastructure changes occur

Deliverables: Structured DevOps analysis (pipeline/container/IaC/deployment/risks) posted on the card/issue.

---

### 14) Project Setup (`project-setup`)
First-use onboarding wizard for new projects.

Responsibilities:
- Detect existing environment before asking questions (Phase 0: manifests, MCP servers, placeholders)
- Collect technology choices from the user (language, framework, database, APM, etc.)
- Collect tooling & integration preferences (issue tracker, MCP servers, doc hosting)
- Update all `[FILL]` placeholders across docs and configuration
- Adapt instruction files (`applyTo` patterns) to match the chosen stack
- Validate that all setup-scope placeholders were replaced (post-apply check)
- Generate **Copilot Productivity Readiness** checklist as final output

Deliverables: Fully configured project with updated docs and readiness checklist.

---

## 🔄 Delegation model

```
User
 │
 ├── pathfinder ──→ Suggests agent flow (optional entry point)
 │
 ├── product-owner ──→ Shows draft and creates/updates card after approval
 │
 ├── architect ──→ Posts architectural analysis on Issue (+ conditional requests to documenter/test-advisor)
 │
 ├── dba ──→ Posts database analysis for tasks with persistence impact
 │
 ├── devops ──→ Posts infra/CI analysis for tasks with infrastructure impact
 │
 ├── staff (orchestrator)
 │     ├── dba (mandatory consultation when DB impact exists)
 │     ├── devops (mandatory consultation when infra/CI impact exists)
 │     ├── backend-dev ──→ Implements backend
 │     │     └── test-advisor (consultation)
 │     ├── frontend-dev ──→ Implements frontend
 │     │     └── test-advisor (consultation)
 │     ├── test-advisor ──→ Proposes testing strategy
 │     ├── metrifier ──→ Suggests metrics
 │     ├── reviewer ──→ Reviews only when code changed
 │     ├── documenter ──→ Documentation mini-plan at start
 │     └── qa ──→ Validates implementation
 │
 ├── reviewer ──→ Reviews PR when code changes exist
 │
 └── documenter ──→ Initial mini-plan + final update
```

---

## 📋 Tracking Protocol

All agents with access to the configured tracker must keep the card updated:

1. **On start**: Comment indicating the agent is acting
2. **Progress**: Update with what's been done and what remains
3. **Subtasks**: Use checklists (`- [ ]`) to track progress
4. **Child items**: Create linked child items for large tasks when the tracker supports it
5. **On completion**: Post summary with PR links and subtask status

Additional `product-owner` rule:
- Always show the complete draft before creating/updating the card
- Always wait for explicit approval
- If there is no write MCP, deliver only the draft ready for manual creation

Standard format:
```markdown
## 🤖 [Agent Name] — Status Update
**Status**: 🟡 In progress / ✅ Completed / 🔴 Blocked

### Subtasks
- [x] Completed task
- [ ] Pending task

### Notes
[Details, decisions, blockers]
```

---

## 🧠 Golden rules

1. **Documentation is the source of truth**
   - If a feature requires a new endpoint/table/entity → update docs first.
2. **Small, incremental changes**
   - Prefer smaller, reviewable PRs.
3. **Respect layers**
   - No business logic in controllers.
   - No direct DB access outside repositories/adapters.
4. **Idempotency and uniqueness**
   - As defined in `docs/domain.md` and `docs/database.md`.
5. **Tests accompany changes**
   - Use cases should have unit tests; critical endpoints, integration tests.
6. **Draft before write**
   - The `product-owner` never creates or updates a card without showing the draft and receiving explicit approval.

---

## 🔁 Main flows

> **Tip:** When the task is uncertain or you don't know where to start, use `pathfinder` first (`/plan-task`) to receive a recommended roadmap before entering any flow below.

### A) New feature
```
(pathfinder) → product-owner → architect → dba(if DB) → devops(if infra/CI) → staff(+documenter-start) → [backend-dev, frontend-dev] → qa → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(optional)*: Diagnoses the task and suggests the ideal agent flow
1. **Product Owner**: Clarifies demand, drafts the card, waits for approval and then creates/updates the item in the tracker when supported
2. **Architect**: Analyzes architectural impact, posts analysis on the approved card
   - If not "already planned" (BOTH), requests in the issue `documenter` (demand documentation) and `test-advisor` (high-level testing)
3. **Staff**: Validates ambiguities, triggers documenter (mini-plan), classifies tests, consults test-advisor, and delegates to BE/FE in parallel when possible
4. **Backend/Frontend**: Implement code and tests
5. **QA**: Executes tests, validates acceptance criteria
6. **Reviewer**: Reviews PR against guidelines when code changes exist
7. **Documenter**: Revalidates impact and updates documentation at closure

### B) Bug fix
```
(pathfinder) → product-owner (clarify) → dba(if DB) → devops(if infra/CI) → staff(+documenter-start) → [backend-dev/frontend-dev] → qa → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(optional)*: Diagnoses the bug and suggests the fix flow
1. **Product Owner**: Clarifies bug report, defines fix criteria, and drafts the card when needed
2. **Staff**: Investigates root cause, validates ambiguities, triggers documenter (mini-plan), classifies as `mudanca_existente`, plans minimal fix and delegates
3. **Backend/Frontend**: Fixes with smallest change possible + adjusts existing tests (or new tests when gaps exist)
4. **QA**: Validates the bug is fixed and there is no regression
5. **Reviewer**: Verifies fix quality when code changes exist
6. **Documenter**: Updates if contract changed

### C) New project (bootstrap)
```
(pathfinder) → product-owner (backlog) → architect (structure) → project-setup (stack config) → staff(+documenter-start, scaffold) → documenter(final)
```

0. **Pathfinder** *(optional)*: Diagnoses project scope and suggests the bootstrap flow
1. **Product Owner**: Defines initial backlog (MVP) in approvable drafts and creates cards when supported
2. **Architect**: Defines architectural style, folder structure, initial ADRs
3. **Project Setup**: Configures technology stack, updates all documentation placeholders, validates readiness
4. **Staff**: Creates project scaffold
5. **Documenter**: Documents structure and decisions

### D) Maintenance / tech debt
```
(pathfinder) → architect → devops(if infra/CI) → staff(+documenter-start) → [backend-dev/frontend-dev] → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(optional)*: Diagnoses impact and suggests the best approach
1. **Architect**: Evaluates architectural impact of the change
2. **Staff**: Plans and delegates implementation
3. **Backend/Frontend**: Implement refactoring
4. **Reviewer**: Validates no architectural violations occurred
5. **Documenter**: Updates affected documentation

---

## ✅ Definition of Done (DoD)

A task is complete when:
- [ ] Documentation mini-plan recorded at start (`required`/`optional`/`none`)
- [ ] Docs updated (if contract/domain/database changed)
- [ ] Implementation respects `docs/architecture.md`
- [ ] API adheres to `docs/api-spec.md`
- [ ] Database validation completed by `dba` when DB changes exist
- [ ] Infra/CI validation completed by `devops` when infrastructure changes exist
- [ ] Testing strategy defined (`feature_nova`/`mudanca_existente`)
- [ ] Tests added/adjusted per strategy
- [ ] Minimum observability (requestId + logs/errors)
- [ ] Quality review completed when code changes exist
- [ ] Tracker card updated with final status
- [ ] PR linked to issue
