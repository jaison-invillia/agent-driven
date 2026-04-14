---
description: "Use when creating or modifying CI/CD pipelines, Dockerfiles, docker-compose files, Terraform modules, Helm charts, or infrastructure-as-code. Covers pipeline security, container best practices, IaC conventions, and deployment safety."
applyTo: "**/.github/workflows/**,**/Dockerfile*,**/docker-compose*,**/terraform/**,**/helm/**"
---

# DevOps & Infrastructure Guidelines

## CI/CD Pipelines (GitHub Actions or equivalent)

### Security
- Pin all third-party actions by full SHA (e.g., `uses: actions/checkout@<sha>`), never by mutable tag alone.
- Never hardcode secrets, tokens, or credentials in workflow files.
- Use GitHub environment secrets, repository secrets, or OIDC for authentication — prefer OIDC for cloud providers.
- Define explicit `permissions` blocks with least-privilege scope on every workflow and job.
- Enable secret scanning and push protection on the repository.

### Structure
- Use concurrency controls (`concurrency:`) to prevent parallel conflicting deployments.
- Implement caching for dependencies and build artifacts (`actions/cache` or equivalent).
- Separate CI (build/test) from CD (deploy) workflows when complexity warrants it.
- Use reusable workflows (`workflow_call`) for shared patterns across repos.
- Set timeouts on jobs to prevent runaway builds.

### Quality
- Include linting, testing, and security scanning steps in CI pipelines.
- Add smoke tests or health checks as post-deployment validation.
- Use status checks and branch protection to gate merges.

---

## Containers (Docker)

### Security
- Run containers as non-root user (`USER` directive in Dockerfile).
- Do not store secrets in image layers — use runtime injection.
- Scan images for vulnerabilities using available tooling.
- Pin base image versions (e.g., `node:20.11-alpine`, not `node:latest`).

### Efficiency
- Use multi-stage builds to minimize final image size.
- Order Dockerfile layers to maximize cache reuse (dependencies before source code).
- Include `.dockerignore` to exclude unnecessary files from build context.

### Reliability
- Define `HEALTHCHECK` in Dockerfile or orchestration configuration.
- Set resource limits (CPU, memory) in orchestration configs (docker-compose, K8s manifests).
- Use `ENTRYPOINT` + `CMD` pattern for flexibility.

---

## Infrastructure as Code (Terraform, Helm, etc.)

### State and safety
- Use remote state with locking (e.g., S3+DynamoDB, GCS, Azure Blob).
- Never commit state files (`.tfstate`) to the repository — add to `.gitignore`.
- Always run `plan` before `apply` — never apply without review.
- Include drift detection strategy in CI/CD.

### Structure
- Organize as reusable modules with clear input/output interfaces.
- Use variable validation and type constraints.
- Separate environment-specific values from module logic (use `.tfvars` or values files).
- Follow naming conventions consistent with `docs/engineer-guidelines.md`.

### Rollback
- Define destroy/rollback procedures for every provisioned resource.
- Use `prevent_destroy` lifecycle rules for critical resources.
- Test rollback procedures as part of infrastructure validation.

---

## Deployment

### Strategy
- Define explicit deployment strategy (blue-green, canary, rolling) based on risk level.
- Include rollback criteria and automated rollback triggers.
- Use health probes (readiness, liveness) for all deployed services.

### Validation
- Include pre-deployment checks (config validation, dependency availability).
- Include post-deployment validation (smoke tests, health endpoint checks).
- Define monitoring alerts for deployment metrics (error rate, latency, availability).

### Environment promotion
- Use consistent promotion flow: dev → staging → production.
- Production deployments require approval gates.
- Environment-specific configuration via environment variables or secrets — never via code changes.

---

## General rules

- All infrastructure/pipeline changes must be idempotent.
- Follow the technology stack documented in `CONTEXT_PACK.md`.
- Respect security rules from `docs/security.md`.
- Respect observability rules from `docs/observability.md`.
- When in doubt, consult the `devops` agent or escalate to human review.
