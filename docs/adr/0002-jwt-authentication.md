# ADR-0002: Use JWT for Authentication

## Status
Accepted

## Context
O Portal Educacional precisa de um mecanismo de autenticação para:

- permitir login de alunos
- proteger rotas da API
- identificar o usuário em operações como progresso de curso e emissão de certificados
- suportar frontend separado (Next.js) consumindo API Node.js

Algumas alternativas consideradas:

1. **Session-based authentication**
   - exige armazenamento de sessão no servidor (memory/Redis)
   - requer cookies e proteção adicional contra CSRF

2. **JWT (JSON Web Token)**
   - stateless
   - simples de usar com APIs REST
   - amplamente suportado em bibliotecas Node.js

3. **OAuth / Identity Provider externo**
   - maior complexidade inicial
   - não necessário para o MVP

Como o sistema inicialmente terá um frontend próprio e não exige SSO ou integração externa, uma solução simples e escalável é preferível.

## Decision
Adotar **JWT (JSON Web Token)** como mecanismo de autenticação da API.

### Funcionamento

1. Usuário realiza login (`POST /auth/login`).
2. A API valida credenciais.
3. A API retorna um **JWT** assinado contendo informações mínimas do usuário.
4. O frontend envia o token em todas requisições protegidas usando:

```
Authorization: Bearer <token>
```

5. Middleware de autenticação valida o token antes de permitir acesso ao endpoint.

### Conteúdo mínimo do token

O JWT deve conter apenas informações essenciais:

```
{
  "sub": "<userId>",
  "email": "<userEmail>",
  "iat": "<issuedAt>",
  "exp": "<expiration>"
}
```

### Expiração

Tokens devem possuir tempo de expiração.

Recomendação inicial:

- `expiresIn`: **1 hora**

Refresh tokens podem ser introduzidos no futuro caso necessário.

### Rotas protegidas

Endpoints que exigem autenticação incluem:

- `GET /courses`
- `GET /courses/{courseId}`
- `POST /courses/{courseId}/lessons/{lessonId}/complete`
- `GET /courses/{courseId}/progress`
- `GET /courses/{courseId}/certificate`

Definições completas em:

- `docs/api-spec.md`

### Segurança

- Tokens devem ser assinados com **secret seguro**.
- Secret deve ser armazenado em **variável de ambiente**.
- Tokens **não devem ser logados**.

Detalhes adicionais:

- `docs/security.md`

## Consequences

### Positive

- Arquitetura **stateless** (API não precisa armazenar sessão).
- Simples integração com frontend (Next.js).
- Escala horizontal sem necessidade de store de sessão.
- Implementação rápida para MVP.

### Negative

- Revogação de token é mais difícil (sem blacklist).
- Logout é principalmente client-side.
- Tokens expostos podem ser usados até expirarem.

### Mitigations

- Usar tempo de expiração curto.
- Introduzir **refresh tokens** se necessário.
- Implementar blacklist/rotation se requisitos de segurança aumentarem.

## Notes

Esta ADR define apenas o **mecanismo de autenticação da API**.

Autorização baseada em roles (RBAC) pode ser introduzida no futuro caso endpoints administrativos sejam adicionados.
