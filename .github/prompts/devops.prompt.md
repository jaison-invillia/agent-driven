---
description: "Trigger the DevOps agent for CI/CD pipeline creation, infrastructure provisioning, container configuration, deployment strategy, or environment setup for an existing GitHub Issue."
agent: "devops"
argument-hint: "Issue number (e.g., #42 or 42)"
---

Perform a complete DevOps analysis and/or implementation for the specified GitHub Issue:

1. Fetch the issue via MCP and read all context/comments
2. Read mandatory documentation (`CONTEXT_PACK.md`, `docs/architecture.md`, `docs/security.md`, `docs/observability.md`, related ADRs)
3. Detect the project's technology stack from `CONTEXT_PACK.md`
4. Identify infrastructure/CI/CD impact (pipelines, containers, IaC, deployment, environment)
5. Design and create/modify resources as needed (GitHub Actions workflows, Dockerfiles, Terraform, Helm charts)
6. Validate security best practices (SHA pinning, no hardcoded secrets, least-privilege, OIDC)
7. Define deployment and rollback strategy
8. Run self-verification checklist (idempotency, health checks, resource limits)
9. Indicate documentation impact and request `documenter` when needed
10. Post the structured DevOps analysis as a comment on the issue via MCP
11. Update issue status with your progress

Follow the DevOps agent workflow strictly.
