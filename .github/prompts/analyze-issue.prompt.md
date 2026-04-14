---
description: "Trigger the Architect agent to perform an architectural analysis of an existing GitHub Issue."
agent: "architect"
argument-hint: "Issue number (e.g., #42 or 42)"
---

Perform a complete architectural analysis for the specified GitHub Issue:

1. Fetch the issue via MCP and read all context (PO notes, acceptance criteria)
2. Read all mandatory documentation (architecture, domain, database, API spec, ADRs)
3. Identify affected architectural layers (per `docs/architecture.md`)
4. Suggest the file/module structure with concrete file names
5. Check if a new ADR is needed
6. Verify whether documentation/testing guidance is already planned using BOTH criteria:
	- relevant subtask in issue checklist
	- prior agent comment requesting/providing guidance
7. If not already planned, request in the issue comment:
	- `documenter` guidance on how to document the demand
	- `test-advisor` guidance on high-level testing strategy
8. Post the structured architectural plan as a comment on the issue via MCP
9. Update the issue with your status

Follow the output format defined in the architect agent instructions.
