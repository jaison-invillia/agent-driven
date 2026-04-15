---
description: "Trigger the DevOps agent to perform an infrastructure and CI/CD impact analysis for an existing GitHub Issue."
agent: "devops"
argument-hint: "Issue number (e.g., #42 or 42)"
---

Perform a complete infrastructure and CI/CD analysis for the specified GitHub Issue:

1. Fetch the issue via MCP and read all context/comments
2. Read mandatory documentation (`CONTEXT_PACK.md`, `docs/architecture.md`, `docs/security.md`, `docs/observability.md`, related ADRs)
3. Detect the project's technology stack from `CONTEXT_PACK.md`
4. Identify infrastructure/CI/CD impact (pipelines, containers, IaC, deployment, environment)
5. Analyze pipeline security (secret management, OIDC, SHA pinning, least-privilege)
6. Evaluate deployment strategy and rollback safety
7. Recommend infrastructure optimizations (caching, concurrency, resource limits)
8. Highlight risks and mitigation strategies
9. Indicate documentation impact and request `documenter` when needed
10. Post the structured DevOps analysis as a comment on the issue via MCP
11. Update issue status with your progress

Follow the DevOps agent workflow strictly.
