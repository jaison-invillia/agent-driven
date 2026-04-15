# ADR-[NUMBER]: [Decision Title]

<!--
## What is an ADR?

ADR (Architecture Decision Record) is a short document that records a significant architectural
decision made in the project, along with the context and consequences of that decision.

### When to create an ADR?
- Choice of architectural style (e.g.: Hexagonal, Modular, MVC, Clean Architecture)
- Choice of database, framework, language
- Authentication/authorization pattern
- Logging/observability strategy
- Any technical decision that affects multiple components or is hard to reverse

### How to use this template?
1. Copy this file as `docs/adr/NNNN-descriptive-title.md` (e.g.: `0001-architectural-style.md`)
2. Replace the `[...]` placeholders with the project's information
3. Fill in all sections
4. Open a PR with the ADR for team review

### Conventions
- Sequential numbering with 4 digits: `0001`, `0002`, etc.
- Filename: `NNNN-descriptive-title.md` in kebab-case
- Directory: `docs/adr/`
- Lifecycle: Proposed → Accepted → Deprecated → Superseded by ADR-NNNN

### Mini-example (delete when creating your ADR)

# ADR-0001: Architectural Style Choice

## Status
Accepted

## Context
The project needs an architecture that maintains separation of concerns,
facilitates testability, and reduces coupling to frameworks.

### Alternatives considered
#### 1. Hexagonal Architecture
- Pros: clear ports & adapters, good testability
- Cons: more initial boilerplate

#### 2. Traditional MVC
- Pros: simplicity, widely known
- Cons: tends to accumulate logic in controllers

## Decision
Adopt [chosen style] with [main justification].

## Consequences
### Positive
- High testability
- Low framework coupling

### Negative
- Higher initial setup cost

### Mitigations
- Document rules in docs/architecture.md
- Enforce boundaries via code review

## Notes
- Impacts: docs/architecture.md, docs/project-structure.md
-->


## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-NNNN]

## Context
[Describe the context and the problem that motivated this decision.
What forces are at play? What constraints exist?
Which alternatives were considered?]

### Alternatives considered

#### 1. [Alternative A]
- [Pros]
- [Cons]

#### 2. [Alternative B]
- [Pros]
- [Cons]

## Decision
[Describe the decision made and the justification.
Be clear and objective.]

## Consequences

### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Trade-off 1]
- [Trade-off 2]

### Mitigations
- [How to mitigate the negative points]

## Notes
- [Additional information, links, references]
- [Documents impacted by this decision]
