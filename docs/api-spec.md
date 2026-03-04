# 📑 API Specification (REST)

Este documento especifica os **contratos da API** do Portal Educacional (REST/JSON).

Referências:
- Visão geral: `README.md`
- Arquitetura: `docs/architecture.md`
- Domínio: `docs/domain.md`
- Banco: `docs/database.md`

---

## ✅ Convenções

### Base URL
- Local: `http://localhost:3001`
- Prefixo: `/api/v1`

Exemplo: `GET /api/v1/courses`

### Formato
- Request/Response: `application/json`
- Datas: ISO 8601 (`YYYY-MM-DDTHH:mm:ss.sssZ`)

### Autenticação
- **JWT Bearer Token**
- Header: `Authorization: Bearer <token>`

### Paginação (quando aplicável)
Parâmetros:
- `page` (default: 1)
- `pageSize` (default: 20, max: 100)

Resposta:
- `meta.page`, `meta.pageSize`, `meta.totalItems`, `meta.totalPages`

### Erros (padrão)
Formato comum:

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

Códigos comuns:
- `VALIDATION_ERROR` → 400
- `UNAUTHORIZED` → 401
- `FORBIDDEN` → 403
- `NOT_FOUND` → 404
- `CONFLICT` → 409
- `INTERNAL_ERROR` → 500

---

## 🔐 Auth

### POST /auth/register
Cria um aluno.

**Auth:** Não

#### Request
```json
{
  "name": "Jaison Schmidt",
  "email": "jaison@email.com",
  "password": "strong_password"
}
```

#### Responses
- **201 Created**
```json
{
  "id": 1,
  "name": "Jaison Schmidt",
  "email": "jaison@email.com",
  "createdAt": "2026-03-04T12:00:00.000Z"
}
```

- **409 Conflict** (email já existe)
- **400 Validation Error**

---

### POST /auth/login
Autentica aluno e retorna token.

**Auth:** Não

#### Request
```json
{
  "email": "jaison@email.com",
  "password": "strong_password"
}
```

#### Response
- **200 OK**
```json
{
  "accessToken": "<jwt>",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": 1,
    "name": "Jaison Schmidt",
    "email": "jaison@email.com"
  }
}
```

- **401 Unauthorized** (credenciais inválidas)
- **400 Validation Error**

---

### GET /me
Retorna informações do usuário autenticado.

**Auth:** Sim

#### Response
- **200 OK**
```json
{
  "id": 1,
  "name": "Jaison Schmidt",
  "email": "jaison@email.com",
  "createdAt": "2026-03-04T12:00:00.000Z"
}
```

- **401 Unauthorized**

---

## 📚 Courses

### GET /courses
Lista cursos disponíveis (somente autenticado).

**Auth:** Sim

#### Query Params (opcional)
- `page`
- `pageSize`

#### Response
- **200 OK**
```json
{
  "data": [
    {
      "id": 10,
      "title": "HTML Básico",
      "description": "Fundamentos de HTML",
      "lessonsCount": 12
    }
  ],
  "meta": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1
  }
}
```

- **401 Unauthorized**

---

### GET /courses/{courseId}
Detalha curso e retorna aulas (ordenadas).

**Auth:** Sim

#### Path Params
- `courseId` (number)

#### Response
- **200 OK**
```json
{
  "id": 10,
  "title": "HTML Básico",
  "description": "Fundamentos de HTML",
  "lessons": [
    {
      "id": 100,
      "title": "Introdução ao HTML",
      "description": "Primeiros passos",
      "youtubeUrl": "https://www.youtube.com/watch?v=xxxx",
      "order": 1
    }
  ]
}
```

- **404 Not Found**
- **401 Unauthorized**

---

## 🎥 Lessons & Progress

### GET /courses/{courseId}/progress
Retorna o progresso do usuário autenticado no curso.

**Auth:** Sim

#### Response
- **200 OK**
```json
{
  "courseId": 10,
  "totalLessons": 12,
  "completedLessons": 3,
  "completionPercentage": 25,
  "lessons": [
    {
      "lessonId": 100,
      "completed": true,
      "completedAt": "2026-03-04T12:10:00.000Z"
    }
  ],
  "certificateAvailable": false
}
```

- **404 Not Found**
- **401 Unauthorized**

---

### POST /courses/{courseId}/lessons/{lessonId}/complete
Marca a aula como concluída para o usuário autenticado.

**Auth:** Sim

#### Response
- **200 OK**
```json
{
  "courseId": 10,
  "lessonId": 100,
  "completed": true,
  "completedAt": "2026-03-04T12:10:00.000Z",
  "courseCompletion": {
    "totalLessons": 12,
    "completedLessons": 4,
    "completionPercentage": 33,
    "certificateAvailable": false
  }
}
```

#### Regras
- Se já estiver concluída, retornar **200** mantendo estado **idempotente**.

#### Erros
- **404 Not Found** (curso/aula inexistentes)
- **403 Forbidden** (aula não pertence ao curso)
- **401 Unauthorized**

---

## 🏆 Certificates

### GET /courses/{courseId}/certificate
Retorna certificado se disponível (usuário autenticado).

**Auth:** Sim

#### Response
- **200 OK**
```json
{
  "courseId": 10,
  "userId": 1,
  "issuedAt": "2026-03-04T13:00:00.000Z",
  "certificateCode": "CERT-ABCD-1234",
  "downloadUrl": "/api/v1/courses/10/certificate/download"
}
```

#### Erros
- **404 Not Found** (não emitido / não disponível)
- **401 Unauthorized**

---

### GET /courses/{courseId}/certificate/download
Baixa o certificado (PDF) se disponível.

**Auth:** Sim

#### Response
- **200 OK**
- Content-Type: `application/pdf`

#### Erros
- **404 Not Found**
- **401 Unauthorized**

---

## 🧪 Admin (futuro / opcional)

> Nesta fase, o projeto pode começar com seed/manual e depois evoluir para endpoints de administração.
> Caso seja necessário, estes endpoints devem ser protegidos por role (RBAC).

- `POST /admin/courses` (criar curso)
- `POST /admin/courses/{courseId}/lessons` (criar aula para curso)

---

## 🔁 Idempotência e Consistência

- `POST /courses/{courseId}/lessons/{lessonId}/complete` deve ser **idempotente**.
- Progresso não deve duplicar (garantia do banco e do service).
- Certificado deve ser **único por usuário/curso** (garantia do banco e do service).

---

## 📌 Checklist de Implementação

- [ ] Middleware JWT (401/403)
- [ ] Validação de input (400)
- [ ] Padronização de erros com `requestId`
- [ ] Paginação em listagens
- [ ] Idempotência na conclusão de aula
- [ ] Geração de PDF do certificado
- [ ] Rota de download protegida
