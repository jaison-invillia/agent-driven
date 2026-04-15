# Agent Squad Usage Guide

Practical guide on how to use the AI agents day-to-day. Each scenario shows **exactly what to type** in VS Code's Copilot Chat and what to expect at each step.

> **Prerequisite**: VS Code with GitHub Copilot active and tracker/tools MCP configured in `.vscode/mcp.json`.

---

## Quick summary

| Command | What it does | Agent |
|---------|-------------|-------|
| `/setup-project` | Configures stack and updates template docs | Project Setup |
| `/plan-task` | Diagnoses task and suggests agent workflow | Pathfinder |
| `/new-feature` | Drafts card and creates after approval | Product Owner |
| `/analyze-issue` | Architectural analysis of an issue | Architect |
| `/analyze-database` | Database impact analysis | DBA |
| `/implement-issue` | Plans and implements an issue | Staff |
| `/fix-bug` | Investigates and fixes a bug | Staff |
| `/review-pr` | Reviews a Pull Request | Reviewer |
| `/document-pr` | Updates docs after merge | Documenter |
| `/devops` | CI/CD pipelines, infrastructure, deployment | DevOps |
| `/analyze-infra` | Infrastructure/CI impact analysis | DevOps |

In addition to commands, you can invoke agents directly by typing `@agent-name` in chat:
`@product-owner`, `@architect`, `@staff`, `@dba`, `@devops`, `@pathfinder`, `@reviewer`, `@documenter`, `@test-advisor`, `@qa`, `@metrifier`, `@project-setup`

---

## Scenario 1 — Configure a new project

Use this flow when configuring the template for the first time.

### Step 0: Configure the stack

```
/setup-project
```

The **Project Setup** will:
- Ask about language, framework, database, APM tools, etc.
- Automatically update all documentation and configuration
- Suggest ADRs for technical decisions

### Step 1: Define the initial backlog

```
/new-feature

I need to create [describe the system and its main features].
Create issues for the main MVP features.
```

The **Product Owner** will:
- Ask clarifying questions (if needed)
- Show the card drafts with acceptance criteria and subtasks
- Create the cards in the tracker only after explicit approval and when a write MCP exists
- Assign priorities (P0–P3)

### Step 2: Define the architecture

```
/analyze-issue #1

Analyze this issue and define the project architecture,
including architectural style, folder structure, and initial ADRs.
```

The **Architect** will:
- Define layers and dependencies
- Suggest folder structure
- Propose ADRs
- Post the analysis as a comment on the issue

### Step 3: Generate the scaffold

```
/implement-issue #1

Create the initial project structure (scaffold) based
on the architectural analysis.
```

The **Staff** will:
- Read the Architect's analysis on the issue
- Plan the file structure
- Delegate to `backend-dev` (backend scaffold) and `frontend-dev` (frontend scaffold)
- Create branch and open PR

### Step 4: Document

```
/document-pr #1

Document the structure created and the decisions made.
```

The **Documenter** will:
- Analyze the PR diff
- Create/update relevant docs (architecture.md, project-structure.md, ADRs)
- Update CONTEXT_PACK.md

---

## Scenario 2 — Create and implement a feature

Full flow: demand → issue → analysis → code → tests → review → docs.

### Step 1: Draft the card

```
/new-feature

[Describe the feature to be implemented,
including business rules and expected behavior.]
```

The **Product Owner** prepares the card with:
- Demand context
- Acceptance criteria (Given/When/Then)
- Priority
- Subtask checklist

Before any remote creation/update, it:
- shows the complete draft in chat
- waits for explicit approval
- if no write MCP exists, delivers the card in `draft-only` mode

### Step 2: Architectural analysis

```
/analyze-issue #42
```

The **Architect** posts on the issue:
- Affected layers (domain, application, infrastructure, interfaces)
- Files to create/modify per layer
- ADR need (if any)
- Risks and attention points
- If high-level documentation/testing is not yet planned, requests in the issue:
	- `documenter`: guidance on how to document the demand
	- `test-advisor`: guidance on high-level testing strategy

> "Already planned" means BOTH are present:
> 1) relevant subtask in the issue checklist and 2) prior agent comment about that guidance.

### Step 3: Implement

```
/implement-issue #42
```

The **Staff** orchestrates everything automatically:
1. Reads the issue (PO context + Architect analysis)
2. Reads mandatory docs (domain, api-spec, database, architecture)
3. Validates ambiguities, task quality, and "already planned" (BOTH) coverage for documentation/high-level testing
4. Triggers `@documenter` for mandatory documentation mini-plan
5. Classifies as `feature_nova` or `mudanca_existente`
6. Consults `@test-advisor` for testing strategy
7. Delegates to `@backend-dev` and `@frontend-dev` in parallel when possible
8. Runs tests to validate
9. Consults `@metrifier` for observability
10. Triggers `@reviewer` only if code changes exist
11. Creates branch and opens PR

> **Tip**: Staff updates the issue with progress at each step.

### Step 4: Review

```
/review-pr #15
```

The **Reviewer** checks:
- Architectural style (no layer violations, per `docs/architecture.md`)
- Adherence to `api-spec.md` and `database.md`
- Security (validation, sensitive data)
- Observability (requestId, logs)
- Test existence

> Execute this step only when code changes exist.

### Step 5: Document

```
/document-pr #15
```

The **Documenter** updates docs if the feature introduced:
- New endpoint → updates `api-spec.md`
- New table/column → updates `database.md`
- New entity → updates `domain.md`
- Architectural decision → creates ADR

### Shortcut: Full cycle in one command

If you prefer to run the entire cycle at once, use the `full-feature-cycle` skill:

```
[Describe the feature to be implemented with business details.]
```

When detecting it's a full feature, Copilot can trigger the skill with PO (draft-first) → Architect → Staff (documenter-start + test classification) → QA → Reviewer (if code changed) → Documenter.

---

## Scenario 3 — Fix a bug

### Option A: Bug already has an issue

```
/fix-bug #55

[Describe the bug: what happens, what should happen.]
```

### Option B: Bug does not have an issue yet

```
/fix-bug

[Describe the bug with details about incorrect behavior
and the expected behavior.]
```

In both cases, the **Staff**:
1. If no issue exists, creates one with the bug context
2. Investigates the root cause (reads code, tests hypotheses)
3. Plans the minimal necessary fix
4. Triggers `@documenter` for mandatory documentation mini-plan
5. Classifies as `mudanca_existente` and consults `@test-advisor`
6. Delegates to `@backend-dev` or `@frontend-dev`
7. Adjusts existing tests when coverage is already sufficient (or adds new ones if gaps exist)
8. Opens PR with the fix

Then, follow the same review and documentation flow:

```
/review-pr #16
/document-pr #16
```

---

## Scenario 4 — Refactoring and tech debt

For technical changes that don't come from a business demand.

### Step 1: Evaluate architectural impact

```
@architect

[Describe the needed refactoring and ask for impact analysis.]
```

The **Architect** evaluates:
- Which layers and files are affected
- Whether the change respects architectural boundaries defined in `docs/architecture.md`
- Whether an ADR is needed

### Step 2: Implement

```
@staff

Implement the refactoring suggested by the Architect.
```

### Step 3: Review and docs

```
/review-pr #17
/document-pr #17
```

---

## Scenario 5 — Diagnose an uncertain task

When you don't know where to start or which agents to involve.

```
/plan-task

[Describe the task, demand, or problem you need to solve.
Include any context about scope, uncertainty, or constraints.]
```

The **Pathfinder** will:
- Ask diagnostic questions to understand the task's nature and scope
- Read existing cards/issues from the tracker for context
- Map the task to the appropriate agents and their optimal execution order
- Suggest a high-level development roadmap
- Identify risks, dependencies, and ambiguities
- Explain why each agent should (or should not) be involved

> **Tip**: Use `/plan-task` as the first step when the task is complex or crosses multiple concerns.

---

## Scenario 6 — Database impact analysis

When a task involves schema changes, migrations, constraints, or query performance.

```
/analyze-database #42

[Describe the database changes needed or reference the issue
with the proposed changes.]
```

The **DBA** will:
- Analyze schema impact for proposed changes
- Recommend constraints, indexes, and migration strategy
- Validate rollback safety (`up`/`down`) and integrity risks
- Keep recommendations engine-agnostic unless project docs specify otherwise
- Post structured analysis on the issue

> **Tip**: Staff automatically consults DBA when database impact is detected during `/implement-issue`.

---

## Scenario 7 — CI/CD and infrastructure

### Option A: Full implementation

When you need to create or modify CI/CD pipelines, Dockerfiles, Terraform, or deployment configurations.

```
/devops

[Describe the infrastructure or CI/CD need, e.g.:
"Create a GitHub Actions CI pipeline for the backend with lint, test, and build steps"
or "Set up a Dockerfile for the backend service"]
```

The **DevOps** will:
- Analyze requirements and constraints
- Design and create the pipeline/infrastructure files
- Review security (secrets, OIDC, least-privilege, SHA pinning)
- Propose deployment strategy
- Define rollback and health check patterns
- Request documentation updates when needed

### Option B: Analysis only

When you need impact analysis before implementation.

```
/analyze-infra #42

[Reference the issue or describe what infrastructure changes
might be needed.]
```

The **DevOps** will:
- Analyze the issue's infrastructure/CI impact
- Identify affected pipelines, containers, or IaC
- Recommend changes without implementing them
- Post structured analysis on the issue

> **Tip**: Staff automatically consults DevOps when infrastructure/CI impact is detected during `/implement-issue`.

---

## Scenario 8 — Consult testing strategy

When you want to know **which tests to write** before implementing.

```
@test-advisor

I'm going to implement [describe the endpoint or feature].
Which tests should I write? Consider unit, integration, and edge cases.
```

The **Test Advisor** returns:
- Unit tests (use case with port mocks)
- Integration tests (repository + database, HTTP endpoint)
- Edge cases (invalid data, missing resource, unauthorized user)
- Mocking strategy
- Needed fixtures

> **Note**: The Test Advisor **proposes** tests but does not write code. Implementation is done by `backend-dev` or `frontend-dev`.

---

## Scenario 9 — Request metrics and observability

```
@metrifier

We're launching [describe the module/feature]. Which metrics and
instrumentation should we add?
```

The **Metrifier** suggests:
- Business metrics (operations per day, conversion rates)
- Technical metrics (latency, error rates)
- Operational metrics (resource usage)
- Recommended alerts
- Dashboard structure

---

## Scenario 10 — Validate an implementation (QA)

After code is implemented but before merge:

```
@qa #42

Validate whether the implementation meets the acceptance criteria of issue #42.
```

The **QA**:
1. Reads the acceptance criteria from the issue
2. Executes automated tests
3. Tests edge cases and security scenarios
4. Posts a structured report on the issue

---

## General tips

- **Document before coding**: Agents work best when `domain.md`, `api-spec.md`, and `database.md` are filled in
- **Use slash commands for formal flows**: `/new-feature`, `/implement-issue`, etc.
- **Use `@agent` for quick consultations**: `@test-advisor`, `@metrifier`, `@architect`
- **Trust Staff to orchestrate**: `@staff` knows how to delegate to the correct sub-agents
- **Update documentation**: Use `/document-pr` after each significant merge
- **Start with `/plan-task` when uncertain**: The Pathfinder will guide you to the right workflow
