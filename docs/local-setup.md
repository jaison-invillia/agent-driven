# 🛠️ Local Setup

<!-- TEMPLATE: Fill this document with your project's setup instructions. -->

This document describes how to prepare and run the project locally (backend + frontend).

References:
- Overview: `README.md`
- Repository structure: `docs/project-structure.md`
- API: `docs/api-spec.md`
- Database: `docs/database.md`

---

## ✅ Prerequisites

### Tools

<!-- [FILL] Define the project prerequisites. Use /setup-project to configure. -->

- **[FILL]** Runtime/language (e.g.: Node.js LTS, Python 3.x, Java 21, Go 1.22)
- **[FILL]** Package manager (e.g.: npm, pnpm, pip, maven, go modules)
- **[FILL]** Database (e.g.: PostgreSQL, MySQL, MongoDB, SQLite)
- Git

### Optional (recommended)
- Docker / Docker Compose (to run dependencies and simplify setup)

---

## 📦 Clone the repository

```bash
git clone <repo-url>
cd <repo-folder>
```

---

## 🗄️ Database

> **[FILL]** Instructions for configuring the project's database.

### Option A) Local installed database

1. Start the database
2. Create the database:

```sql
CREATE DATABASE [database_name];
```

### Option B) Docker (recommended)

> **[FILL]** Add or reference the project's `docker-compose.yml`.

```bash
docker compose up -d
```

---

## ⚙️ Environment variables

### Backend

Create the backend environment variables file based on `.env.example`:

> **[FILL]** List the required environment variables.

```env
# Server
PORT=[FILL]
ENV=development

# Database
DB_HOST=localhost
DB_PORT=[FILL]
DB_NAME=[database_name]
DB_USER=[FILL]
DB_PASSWORD=[FILL]

# Auth
# [FILL] Authentication variables (e.g.: JWT_SECRET, API_KEY, etc.)

# Observability
# [FILL] Observability variables (e.g.: APM_ENABLED, APM_LICENSE_KEY, etc.)
```

### Frontend

Create the frontend environment variables file:

```env
# [FILL] Frontend variables
API_BASE_URL=http://localhost:[FILL]/api/v1
```

---

## 🔧 Backend

```bash
cd [backend-directory]
# [FILL] Command to install dependencies (e.g.: npm install, pip install -r requirements.txt)
```

### Run migrations

> The migration tool will be defined during implementation.
> The reference schema is in `docs/database.md`.

```bash
# [FILL] Migration command (e.g.: npm run migrate, alembic upgrade head, flyway migrate)
```

### Run seeds (optional)

> **[FILL]** Add seed instructions when available.

```bash
# [FILL] Seed command
```

### Start

```bash
# [FILL] Command to start the backend (e.g.: npm run dev, python manage.py runserver)
```

Backend available at `http://localhost:[FILL]`.

---

## 🌐 Frontend

```bash
cd [frontend-directory]
# [FILL] Command to install dependencies
# [FILL] Command to start the frontend
```

Frontend available at `http://localhost:[FILL]`.

---

## ✅ Verify

- Backend: `http://localhost:[FILL]/health`
- Frontend: `http://localhost:[FILL]`

---

## 🐛 Troubleshooting

| Problem | Solution |
|---------|----------|
| Database connection error | Check if the database is running and credentials in `.env` |
| Port in use | Change `PORT` in `.env` |
| Migrations fail | Check if the database was created and credentials are correct |

### Port in use
- Backend: adjust `PORT` in the environment variables file
- Frontend: adjust the port per the framework

### Database connection error
Check:
- Database is running
- Credentials in the environment variables file
- Database port is accessible

### Invalid JWT
Check:
- Authentication secret configured in the backend
- Token in `Authorization` header

---

## 📌 Where to save

Recommended:
- `docs/local-setup.md`
