---
description: "Start the bug fix flow: the Staff orchestrator investigates, plans the fix, delegates implementation, and opens a PR."
agent: "staff"
argument-hint: "Issue number of the bug (e.g., #42 or 42)"
---

Fix the bug described in the specified GitHub Issue:

1. Fetch the issue and understand the bug report (steps to reproduce, expected vs actual)
2. Read relevant documentation and existing code
3. Investigate root cause — use logs, tests, and code analysis
4. Clarify ambiguities and validate task quality before implementation
5. Trigger documenter for mandatory mini documentation plan at task start
6. Classify as `mudanca_existente` and consult test-advisor
7. Plan the minimal fix (smallest change possible)
8. Delegate fix implementation to backend-dev and/or frontend-dev
9. Adjust existing tests when coverage is already sufficient (add new tests if gaps exist)
10. Validate the fix and run full test suite
11. Trigger reviewer only if code changes exist
12. Open a PR via MCP referencing the bug issue
13. Post status update on the issue

Follow the bug fix flow from `docs/agent-task-flow.md`:
- Minimize change scope
- Prefer adjusting existing tests when coverage is already sufficient
- Verify no architectural violations
