---
name: full-feature-cycle
description: "Complete feature lifecycle: from demand to merged PR with documentation. Orchestrates PO → Architect → Staff (with documenter start + test classification) → BE/FE → QA → Reviewer (code-change only) → Documenter final. Use for end-to-end feature delivery."
argument-hint: "Describe the feature or provide the issue number"
---

# Full Feature Cycle Skill

End-to-end workflow for delivering a complete feature, from initial demand to merged, documented PR.

## When to Use

- Delivering a complete feature end-to-end
- Running the full agent pipeline for a single feature
- Ensuring nothing is missed in the development lifecycle

## Procedure

### Phase 1 — Triage (PO + Architect)

1. Invoke `product-owner` agent with the feature demand
   - Creates issue with acceptance criteria, priority, subtasks
2. Invoke `architect` agent with the issue number
   - Posts architectural analysis and file structure
   - Identifies if ADR is needed

### Phase 2 — Implementation (Staff + BE/FE)

3. Invoke `staff` agent with the issue number
   - Clarifies ambiguities and validates task quality
   - Triggers `documenter` for mandatory mini documentation plan
   - Classifies testing approach as `feature_nova` or `mudanca_existente`
   - Plans implementation at code level
   - Consults `test-advisor` for testing strategy by classification
   - Delegates to `backend-dev` and/or `frontend-dev`
   - Parallelizes independent delegations whenever possible
   - Sub-agents implement code and tests
   - Staff validates and opens PR

### Phase 3 — Quality (QA + Reviewer)

4. Invoke `qa` agent with the issue/PR number
   - Runs automated tests
   - Validates acceptance criteria
   - Posts QA report

5. Invoke `reviewer` agent with the PR number
   - Only when there are code changes
   - Reviews code against all guidelines
   - Posts review with approve/request changes

### Phase 4 — Documentation (Documenter)

6. Invoke `documenter` at task start (mini-plan) and again after implementation with PR number
   - Start: classify doc impact (`required`/`optional`/`none`)
   - Updates affected documentation
   - Creates ADR if architectural decisions were made
   - Updates CONTEXT_PACK if significant

### Phase 5 — Metrics (Metrifier — optional)

7. Optionally invoke `metrifier` for observability recommendations
   - Suggests metrics to collect
   - Recommends instrumentation approach

## Completion Checklist

Refer to the [full checklist](./references/checklist.md) for the Definition of Done.

## Important Notes

- Each phase depends on the previous one completing successfully
- If any phase fails or is blocked, resolve before proceeding
- The issue card should be updated by each agent throughout
- All work must respect project documentation as source of truth
