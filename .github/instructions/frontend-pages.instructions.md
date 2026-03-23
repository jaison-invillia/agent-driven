---
description: "Use when creating or modifying frontend pages, components, or hooks. Covers Next.js patterns, SSR, API integration, and UI state handling."
applyTo: "frontend/**/*.{tsx,ts}"
---

# Frontend Development Guidelines

## Next.js patterns (per ADR-0006)

- **SSR** (`getServerSideProps`): Use for dynamic, user-specific, authenticated pages
- **SSG** (`getStaticProps`): Use for public, rarely-changing content
- Default to SSR for authenticated routes

## Project structure

```
frontend/
  pages/          # Next.js route pages
  components/     # Reusable UI components
  hooks/          # Custom React hooks
  services/       # API client functions
  styles/         # CSS/styling
  utils/          # Helpers
  types/          # TypeScript types
```

## API integration

- All API calls go through `services/` — never call endpoints directly from components
- Match contracts exactly as defined in `docs/api-spec.md`
- Base path: `/api/v1`
- Handle JWT token: attach to requests via Authorization header
- Handle error response format: `{ code, message, details, requestId }`

## UI state handling

Every data-fetching component MUST handle:
- **Loading**: skeleton/spinner while fetching
- **Error**: meaningful error message with retry option
- **Empty**: appropriate empty state message
- **Success**: render data
- **Token expired**: redirect to login

## Non-negotiable rules

- **No business rules in frontend** — backend is source of truth
- Never expose JWT tokens in client-side errors or console
- Always handle HTTP errors gracefully
- Use TypeScript types for all component props and API responses
