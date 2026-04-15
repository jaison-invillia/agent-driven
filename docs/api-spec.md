# 📑 API Specification (REST)

<!-- TEMPLATE: Fill this document with your project's API contracts. -->
<!-- See docs/engineering-docs-recommendation.md for detailed guidance. -->

This document specifies the project's **API contracts** (REST/JSON).

References:
- Overview: `README.md`
- Architecture: `docs/architecture.md`
- Domain: `docs/domain.md`
- Database: `docs/database.md`

---

## ✅ Conventions

### Base URL
- Local: `http://localhost:3001`
- Prefix: `/api/v1`

Example: `GET /api/v1/[resource]`

### Format
- Request/Response: `application/json`
- Dates: ISO 8601 (`YYYY-MM-DDTHH:mm:ss.sssZ`)

### Authentication

> **[FILL]** Define the authentication scheme (e.g.: JWT Bearer, API Key, OAuth2).

- Header: `Authorization: Bearer <token>`

### Pagination (when applicable)
Parameters:
- `page` (default: 1)
- `pageSize` (default: 20, max: 100)

Response:
- `meta.page`, `meta.pageSize`, `meta.totalItems`, `meta.totalPages`

### Errors (standard)
Common format:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      { "field": "email", "message": "Email is invalid" }
    ],
    "requestId": "req_123"
  }
}
```

Common codes:
- `VALIDATION_ERROR` → 400
- `UNAUTHORIZED` → 401
- `FORBIDDEN` → 403
- `NOT_FOUND` → 404
- `CONFLICT` → 409
- `INTERNAL_ERROR` → 500

---

## 🔐 Auth

> **[FILL]** Document your project's authentication endpoints.

### POST /auth/register

**Auth:** No

#### Request
```json
{
  "name": "string",
  "email": "string",
  "password": "string"
}
```

#### Responses
- **201 Created** — User created
- **409 Conflict** — Email already exists
- **400 Validation Error**

---

### POST /auth/login

**Auth:** No

#### Response
- **200 OK** — Access token returned
- **401 Unauthorized** — Invalid credentials

---

## 📦 Resources

> **[FILL]** Document the endpoints for each system resource following the pattern below.

### GET /[resource]

> **[FILL]** Describe the endpoint.

**Auth:** Yes/No

#### Query Params (optional)
- `page`, `pageSize`

#### Response
- **200 OK**

---

### GET /[resource]/{id}

> **[FILL]** Describe the endpoint.

**Auth:** Yes/No

#### Path Params
- `id` (number)

#### Response
- **200 OK**
- **404 Not Found**

---

### POST /[resource]

> **[FILL]** Describe the endpoint.

**Auth:** Yes/No

#### Request
```json
{}
```

#### Response
- **201 Created**
- **400 Validation Error**

---

## 🔁 Idempotency and Consistency

> **[FILL]** List endpoints that should be idempotent and uniqueness rules that the backend must enforce.

---

## 📌 Implementation Checklist

- [ ] Authentication middleware (401/403)
- [ ] Input validation (400)
- [ ] Error standardization with `requestId`
- [ ] Pagination on listings
- [ ] Idempotent endpoints where necessary
