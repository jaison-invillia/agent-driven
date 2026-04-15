# 📈 Observability

This document defines the project's **observability** standard: logs, correlation, errors, metrics, and instrumentation.

References:
- Architecture: `docs/architecture.md`
- Guidelines: `docs/engineer-guidelines.md`
- Standard API errors: `docs/api-spec.md`
- AI context: `docs/ai/ai-context.md`

---

## 🎯 Goals

- Detect and diagnose problems quickly (low MTTR).
- Correlate logs ↔ traces ↔ errors using a unique identifier.
- Standardize log format to facilitate search and dashboards.
- Ensure minimum visibility: latency, error rate, throughput.

---

## ✅ Correlation standard

### requestId (required)
- Generate a unique `requestId` for **every request**.
- Prefer accepting `X-Request-Id` from the client/gateway; if absent, generate one.
- Always return the `requestId` in the response header: `X-Request-Id`.
- Include `requestId` in:
  - logs
  - error responses (per `docs/api-spec.md`)
  - APM tool custom attributes

**Standard header**
- Input: `X-Request-Id`
- Output: `X-Request-Id`

---

## 🪵 Logging

### Minimum requirements
- Structured logs in **JSON**.
- Levels: `debug`, `info`, `warn`, `error`.
- Log only what is necessary (avoid noise).
- Never log sensitive data:
  - password, `password_hash`
  - full JWT token
  - personal data beyond what is needed (avoid PII)

### Standard log fields (backend)
Every log should try to contain:

```json
{
  "timestamp": "ISO-8601",
  "level": "info",
  "message": "human readable message",
  "service": "[project-name]-api",
  "env": "dev|staging|prod",
  "requestId": "req_...",
  "http": {
    "method": "GET",
    "path": "/api/v1/[resource]",
    "status": 200,
    "durationMs": 34
  },
  "user": {
    "id": 1
  },
  "error": {
    "name": "ErrorName",
    "message": "error message",
    "stack": "stacktrace (prod: optional/limited)",
    "code": "INTERNAL_ERROR"
  }
}
```

### What to log (minimum)
- **Request start** (optional in prod, useful in dev/staging)
- **Request end** (recommended)
  - method, path, status, durationMs, requestId
- **Errors** (always)
  - requestId + code + stack (with care in prod)
- **Relevant domain events** (info)
  > **[FILL]** List domain events that should be logged (without PII)

### Where to log
- Backend: stdout/stderr (for later aggregation)
- Frontend: avoid noisy logs in prod; use only critical errors

---

## ❗ Errors and exceptions

### Error response standard
All errors must follow the format defined in `docs/api-spec.md`:

- `code`
- `message`
- `details` (when validation)
- `requestId`

### Recommended mapping (backend)
- Input validation → 400 (`VALIDATION_ERROR`)
- No auth/invalid token → 401 (`UNAUTHORIZED`)
- No permission → 403 (`FORBIDDEN`)
- Resource not found → 404 (`NOT_FOUND`)
- Conflict (duplicate resource) → 409 (`CONFLICT`)
- Unexpected error → 500 (`INTERNAL_ERROR`)

**Rule:** unexpected error must:
- log an `error` with requestId
- report to the APM tool
- return `INTERNAL_ERROR` without sensitive details

---

## 🧪 Healthchecks

### Recommended endpoints
- `GET /health` → liveness (API is up)
- `GET /ready` → readiness (dependencies OK: DB)

Response example:

```json
{
  "status": "ok",
  "service": "[project-name]-api",
  "version": "git_sha_or_semver",
  "uptimeSeconds": 12345
}
```

For readiness, include dependencies:

```json
{
  "status": "ok",
  "dependencies": {
    "database": "ok"
  }
}
```

---

## 📊 Metrics (minimum viable)

### API metrics
- Latency p50/p95/p99 per endpoint
- Error rate (4xx/5xx)
- Throughput (req/min)
- Apdex (if configured)

### Domain metrics (custom)

> **[FILL]** Define project-specific business metrics. Examples:
> - Completed operations per day
> - Conversion rate
> - Items processed per hour

**Note:** avoid metrics that expose PII.

---

## 🧩 Tracing (APM)

<!-- [FILL] Define the project's APM tool (e.g.: Datadog, New Relic, Grafana, OpenTelemetry). -->

### Requirements
- APM instrumentation enabled on the backend.
- Include custom attributes:
  - `requestId`
  - `userId` (when authenticated)
  - Resource IDs (only when relevant)
- Name transactions by route (e.g.: `GET /api/v1/[resource]/{id}`)

### Important events for tracing
- Login (success/failure) — with care not to log credentials
- Relevant write operations

> **[FILL]** List domain events relevant to tracing.

---

## 🔎 Alerts (baseline)

Configure basic alerts in staging/prod:

- **High Error Rate**: 5xx above X% for Y minutes
- **High Latency**: p95 above X ms for Y minutes
- **DB Connectivity**: readiness failing
- **Crash/Restart loop**: app restarting continuously

(Thresholds will be defined after observing actual baseline.)

---

## 🧰 Quick troubleshooting

> **[FILL]** Add project-specific troubleshooting scenarios. Template:

### Scenario: [problem description]
Check:
1. Relevant endpoint and response
2. Logs with `requestId`
3. Database constraints
4. Trace in APM tool

---

## 🤖 Rules for AI

- Do not invent log fields outside the standard without updating this document.
- Do not add PII to logs.
- Always include `requestId` in logs and error payloads.
- If changing error format, update `docs/api-spec.md` and this document.
