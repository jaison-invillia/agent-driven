# CONTEXT_PACK.md

This document provides a **condensed context snapshot** of the project so AI assistants
(GitHub Copilot, coding agents, LLMs) can quickly understand the system without scanning
the entire documentation.

Full documentation lives in `/docs`. If something here conflicts with `/docs`, the docs
directory is the **source of truth**.

---

# Project Overview

Project: Educational Portal

Purpose:
Allow students to register, watch course lessons hosted on YouTube, track progress,
and receive certificates upon course completion.

Core user flow:

Register → Login → Browse Courses → Watch Lessons → Mark Lesson Complete → Receive Certificate

---

# Technology Stack

Backend
- Node.js
- Clean Architecture
- REST API

Frontend
- Next.js
- Server Side Rendering (SSR)

Database
- MySQL 8

Monitoring
- New Relic

Video Hosting
- YouTube

---

# Architectural Style

Backend follows **Clean Architecture**.

Layers:

domain
application
interfaces
infrastructure
main

Dependency direction:

interfaces → application → domain
infrastructure → application → domain
main → all layers (composition root only)

Rules:

- Domain must not depend on frameworks or infrastructure
- Controllers must not contain business logic
- Database access must happen through repositories
- Use cases live in the application layer

Reference:
docs/architecture.md
ADR-0001

---

# Core Domain Model

Main entities:

User
Course
Lesson
CourseProgress
Certificate

Relationships:

User → completes → Lesson
Lesson → belongs to → Course
User → progresses through → Course
Course → grants → Certificate

Reference:
docs/domain.md

---

# Course Completion Logic

A course is considered **completed** when:

completed_lessons == total_lessons

Progress tracking is **per lesson**.

Lesson completion endpoint must be **idempotent**.

Reference:
ADR-0003

---

# Certificate Logic

Certificates are generated **on demand as PDF**.

Rules:

- Only available when course is completed
- Unique per (user, course)
- Stored metadata only (PDF is not persisted)

Reference:
ADR-0007

---

# Database Overview

Primary tables:

users
courses
lessons
course_progress
certificates

Important constraints:

course_progress:
UNIQUE(user_id, lesson_id)

certificates:
UNIQUE(user_id, course_id)

Reference:
docs/database.md
ADR-0004

---

# API Overview

Base path:

/api/v1

Authentication:

JWT Bearer Token

Authorization header:

Authorization: Bearer <token>

Important endpoints:

POST /auth/register
POST /auth/login

GET /courses
GET /courses/{courseId}

POST /courses/{courseId}/lessons/{lessonId}/complete

GET /courses/{courseId}/progress

GET /courses/{courseId}/certificate
GET /courses/{courseId}/certificate/download

Reference:
docs/api-spec.md
ADR-0002

---

# Observability

Logging rules:

- Structured logs
- JSON format
- Include requestId in every request

Error responses must include:

requestId

Monitoring:

New Relic

Reference:
docs/observability.md
ADR-0008

---

# Security Rules

Never:

- log passwords
- log password hashes
- log full JWT tokens

Always:

- validate input
- protect user scoped resources
- prevent SQL injection

Reference:
docs/security.md

---

# AI / Agent Rules

AI assistants must:

- Read AGENTS.md
- Read docs before implementing features
- Never invent API endpoints
- Never invent database tables
- Respect Clean Architecture boundaries

Preferred workflow:

1. Check domain rules
2. Check API contract
3. Check database schema
4. Implement backend layers
5. Implement frontend integration
6. Add tests
7. Update docs if necessary

---

# Key ADR Decisions

ADR-0001 Clean Architecture
ADR-0002 JWT Authentication
ADR-0003 Progress per Lesson
ADR-0004 MySQL Database
ADR-0005 YouTube Video Hosting
ADR-0006 Next.js SSR
ADR-0007 Certificate PDF On Demand
ADR-0008 Structured Logging

---

# Important Documentation

For deeper context:

docs/architecture.md
docs/domain.md
docs/database.md
docs/api-spec.md
docs/security.md
docs/observability.md
docs/project-structure.md
docs/engineering-guidelines.md
