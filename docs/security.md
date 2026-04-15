# 🔒 Security

This document defines the project's **security** baseline: authentication, authorization, validation, data protection, and secure development practices.

References:
- Architecture: `docs/architecture.md`
- Guidelines: `docs/engineer-guidelines.md`
- Contracts/API: `docs/api-spec.md`
- Database: `docs/database.md`
- Observability: `docs/observability.md`

---

## 🎯 Goals

- Protect user accounts and data.
- Avoid common vulnerability classes (OWASP Top 10).
- Define a security baseline for incremental implementation.
- Minimize data (PII) and credential leakage.

---

## ✅ Principles

- **Secure by default**: sensitive routes protected by default.
- **Least privilege**: minimum necessary permissions.
- **Defense in depth**: multiple layers of protection (auth + validation + DB constraints).
- **Don't trust the client**: all critical validation happens on the backend.

---

## 🔐 Authentication (JWT)

### Rules
- Protected routes require `Authorization: Bearer <token>`.
- Invalid/missing tokens → **401 UNAUTHORIZED**.
- Never log the full token (at most prefix + hash/truncated, if necessary).
- Passwords are never stored in plain text:
  - store only `password_hash` (recommended secure algorithm, e.g.: bcrypt, argon2, scrypt).

### Expiration and rotation (baseline)
- `expiresIn` on login (e.g.: 1h).
- Refresh token is optional for MVP; if introduced, document in `docs/api-spec.md`.

### Logout
- For MVP (stateless JWT), logout is client-side (remove token).
- If blacklist/rotation is needed, introduce a store (e.g.: Redis) and document it.

---

## 🛂 Authorization (Access Control)

### Minimum rules
- User can only access data from their **own** context.
- Current endpoints are "user scoped".
- Admin endpoints (future) must require RBAC.

### Admin (future)
- Introduce `role` on user (e.g.: `user`, `admin`) **only when necessary**.
- Document RBAC in `docs/domain.md` and `docs/api-spec.md`.

---

## ✅ Input Validation and Sanitization

### Rules
- Validate input on **all** endpoints.
- Return 400 with `VALIDATION_ERROR` per `docs/api-spec.md`.
- Sanitize strings when applicable (e.g.: trim, length limits).
- Reject payloads with unexpected fields (when possible).

### Minimum validation examples
- `email` valid format and maximum length
- `password` minimum and maximum length
- Numeric IDs validated (e.g.: `entityId`)
- URLs should be validated when applicable

---

## 🧱 Injection Protection

### SQL Injection
- Use parameterized queries/ORM.
- Never concatenate user input in SQL.
- Ensure constraints and FKs per `docs/database.md`.

### Other injections
- Never render HTML from user inputs without sanitization (frontend).
- Avoid SSRF (if in the future there is fetching of external URLs).

---

## 🧩 CSRF, CORS, and Cookies

### Token in header (baseline)
- JWT in `Authorization` reduces CSRF risk (compared to cookie auth).
- If cookies are used in the future:
  - `HttpOnly`, `Secure`, `SameSite=Lax/Strict`
  - implement CSRF tokens

### CORS
- Configure origin allowlist (staging/prod).
- Do not use `*` in production.

---

## 🧠 Rate Limiting (recommended)

Optional for MVP, but recommended in production:
- `/auth/login`: limit attempts per IP/user
- `/auth/register`: limit creation per IP
- response: 429 `TOO_MANY_REQUESTS` (if adopted, document in API spec)

---

## 🔑 Secrets Management

### Rules
- Never commit secrets to the repository.
- Use environment variables.
- Separate configs per environment (dev/staging/prod).
- Rotate tokens/keys when exposed.

### Secret examples
- DB password
- Authentication secret / private keys
- APM/observability tool license keys

---

## 🧾 Logging and PII

### Rules
- Do not log:
  - password, password_hash
  - full JWT token
  - complete payloads on sensitive routes
- Logs must contain `requestId` for correlation (see `docs/observability.md`).
- In errors, avoid returning stack traces to the client.

PII (minimum):
- In logs, prefer `userId` over email/name.

---

## 📦 Dependencies and Supply Chain

### Rules
- Keep dependencies updated (frequent patch/minor).
- Enable vulnerability scanning (e.g.: GitHub Dependabot, Snyk, GHAS, or equivalent).
- Validate licenses when necessary.

### Package manager hardening
- Block dangerous scripts when possible (if applicable to the package manager used).
- Review critical transitive dependencies.

---

## 🧪 Security checklist per endpoint (baseline)

### Auth
- Register:
  - email/password validation
  - avoid user enumeration in messages
- Login:
  - generic response for invalid credentials
  - rate limiting recommended

### Protected resources
- require valid token
- validate resource IDs
- ensure referential integrity between entities
- idempotency on write operations (when applicable)
- access restricted to the resource owner

---

## 🧯 Security Testing (minimum)

- Unit tests for critical validations
- Integration tests for:
  - 401 without token
  - 403 when resource does not belong
  - 409 on conflict (e.g.: duplicate email)
- Regression test for authorization bugs (IDOR)

---

## 🤖 Rules for AI

- Do not introduce new endpoints without updating `docs/api-spec.md`.
- Do not create fields/tables without updating `docs/domain.md` and `docs/database.md`.
- Implement input validations on all endpoints.
- Never generate code that logs secrets or improper PII.
- Any auth change must be reflected in this document.
