---
description: "Use when creating or modifying API controllers, routes, or DTOs. Covers response format, input validation, error handling, and observability."
applyTo: "backend/src/interfaces/**/*.ts"
---

# API Controller Guidelines

## Response format

All endpoints must return responses following `docs/api-spec.md`:

```typescript
// Success
{ data: T, requestId: string }

// Error
{ code: string, message: string, details?: any, requestId: string }
```

## HTTP status codes

| Code | When |
|------|------|
| 200 | Successful GET, PUT, PATCH |
| 201 | Successful POST (resource created) |
| 400 | Validation error (`VALIDATION_ERROR`) |
| 401 | Missing or invalid auth token (`UNAUTHORIZED`) |
| 403 | Insufficient permissions (`FORBIDDEN`) |
| 404 | Resource not found (`NOT_FOUND`) |
| 409 | Conflict / duplicate (`CONFLICT`) |
| 500 | Unexpected error (`INTERNAL_ERROR`) |

## Input validation

- Validate ALL inputs at the controller boundary
- Use schema validators (Zod, Joi, or equivalent)
- Return 400 with specific field errors
- Never trust client-side validation

## Controller rules

- **No business logic** — controllers only parse, validate, call use case, format response
- Inject use cases via constructor/DI
- Always propagate `requestId` from `X-Request-Id` header
- Include `requestId` in every error response

## Error handling

- Catch use-case errors and map to appropriate HTTP status
- Never expose stack traces to the client
- Log errors with `requestId` for tracing
- Provide actionable error messages

## Security

- Never log passwords, password hashes, or full JWT tokens
- Sanitize user input before processing
- Use parameterized queries / ORM protections
