# 🛠️ Local Setup

Este documento descreve como preparar e executar o Portal Educacional localmente (backend + frontend).

Referências:
- Visão geral: `README.md`
- Estrutura do repositório: `docs/project-structure.md`
- API: `docs/api-spec.md`
- Banco: `docs/database.md`

---

## ✅ Pré-requisitos

### Ferramentas
- **Node.js** (LTS recomendado)
- **npm** ou **pnpm** (usar um padrão no repo)
- **MySQL 8**
- Git

### Opcional (recomendado)
- Docker / Docker Compose (para subir MySQL e facilitar setup)

---

## 📦 Clonar o repositório

```bash
git clone <repo-url>
cd <repo-folder>
```

---

## 🗄️ Banco de dados (MySQL 8)

### Opção A) MySQL local instalado

1. Inicie o MySQL 8
2. Crie o banco:

```sql
CREATE DATABASE educational_portal CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

3. Crie um usuário (opcional):

```sql
CREATE USER 'edu_user'@'%' IDENTIFIED BY 'edu_pass';
GRANT ALL PRIVILEGES ON educational_portal.* TO 'edu_user'@'%';
FLUSH PRIVILEGES;
```

### Opção B) Docker (recomendado)

Crie um `docker-compose.yml` (ou utilize um existente) com MySQL 8:

```yaml
version: "3.9"
services:
  mysql:
    image: mysql:8
    container_name: educational-portal-mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: educational_portal
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

Suba:

```bash
docker compose up -d
```

---

## ⚙️ Variáveis de ambiente

### Backend

Crie o arquivo:

- `backend/.env`

Baseie-se em um `.env.example` (recomendado criar no repo). Exemplo mínimo:

```env
# Server
PORT=3001
NODE_ENV=development

# Database
DB_HOST=localhost
DB_PORT=3306
DB_NAME=educational_portal
DB_USER=root
DB_PASSWORD=root

# Auth
JWT_SECRET=change_me_dev_secret
JWT_EXPIRES_IN=3600

# Observability
NEW_RELIC_ENABLED=false
NEW_RELIC_LICENSE_KEY=
```

### Frontend

Crie o arquivo:

- `frontend/.env.local`

Exemplo mínimo:

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:3001/api/v1
```

---

## 🔧 Backend

Entre na pasta:

```bash
cd backend
```

Instale dependências:

```bash
npm install
```

### Rodar migrations

> A ferramenta de migration (ex.: Prisma, Knex, TypeORM, Flyway etc.) será definida na implementação.
> Neste momento, o schema referência está em `docs/database.md`.

Estratégia recomendada quando o migrator existir:

```bash
npm run db:migrate
```

### Seed (opcional)

Para desenvolvimento, pode existir um seed com:
- 1 curso
- 3+ aulas
- 1 usuário demo

Exemplo (quando implementado):

```bash
npm run db:seed
```

### Rodar o backend

```bash
npm run dev
```

Backend esperado:
- Base URL: `http://localhost:3001`
- Health: `GET /health` (se implementado)

---

## 🌐 Frontend (Next.js)

Abra outra aba/terminal e entre na pasta:

```bash
cd frontend
```

Instale dependências:

```bash
npm install
```

Rodar o frontend:

```bash
npm run dev
```

Frontend esperado:
- URL: `http://localhost:3000`

---

## ✅ Teste rápido do fluxo (MVP)

1. Registrar usuário:
   - `POST /api/v1/auth/register`

2. Login:
   - `POST /api/v1/auth/login`
   - Copiar `accessToken`

3. Listar cursos:
   - `GET /api/v1/courses` com header `Authorization: Bearer <token>`

4. Detalhar curso:
   - `GET /api/v1/courses/{courseId}`

5. Marcar aula como concluída:
   - `POST /api/v1/courses/{courseId}/lessons/{lessonId}/complete`

6. Progresso:
   - `GET /api/v1/courses/{courseId}/progress`

7. Certificado:
   - `GET /api/v1/courses/{courseId}/certificate`
   - `GET /api/v1/courses/{courseId}/certificate/download`

Contratos completos em:
- `docs/api-spec.md`

---

## 🧯 Troubleshooting

### Porta em uso
- Backend: ajuste `PORT` no `backend/.env`
- Frontend: ajuste porta do Next se necessário

### Erro de conexão com DB
Verifique:
- MySQL rodando
- credenciais em `backend/.env`
- porta `3306` liberada

### JWT inválido
Verifique:
- `JWT_SECRET` no backend
- token no header `Authorization`

---

## 📌 Onde salvar

Recomendado:
- `docs/local-setup.md`
