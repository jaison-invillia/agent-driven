# 📈 Observability

Este documento define o padrão de **observabilidade** do Portal Educacional: logs, correlação, erros, métricas e instrumentação com New Relic.

Referências:
- Arquitetura: `docs/architecture.md`
- Guidelines: `docs/engineering-guidelines.md`
- API errors padrão: `docs/api-spec.md`
- AI context: `docs/ai-context.md`

---

## 🎯 Objetivos

- Detectar e diagnosticar problemas rapidamente (MTTR baixo).
- Correlacionar logs ↔ traces ↔ erros usando um identificador único.
- Padronizar o formato de logs para facilitar busca e dashboards.
- Garantir visibilidade mínima: latência, taxa de erro, throughput.

---

## ✅ Padrão de correlação

### requestId (obrigatório)
- Gerar um `requestId` único para **toda requisição**.
- Preferir aceitar `X-Request-Id` vindo do client/gateway e, se não existir, gerar.
- Retornar sempre o `requestId` no response header: `X-Request-Id`.
- Incluir `requestId` em:
  - logs
  - respostas de erro (conforme `docs/api-spec.md`)
  - atributos do New Relic (custom attributes)

**Header padrão**
- Entrada: `X-Request-Id`
- Saída: `X-Request-Id`

---

## 🪵 Logging

### Requisitos mínimos
- Logs estruturados em **JSON**.
- Níveis: `debug`, `info`, `warn`, `error`.
- Logar apenas o necessário (evitar ruído).
- Nunca logar dados sensíveis:
  - senha, `password_hash`
  - token JWT completo
  - dados pessoais além do necessário (evitar PII)

### Campos padrão do log (backend)
Todo log deve tentar conter:

```json
{
  "timestamp": "ISO-8601",
  "level": "info",
  "message": "human readable message",
  "service": "educational-portal-api",
  "env": "dev|staging|prod",
  "requestId": "req_...",
  "http": {
    "method": "GET",
    "path": "/api/v1/courses",
    "status": 200,
    "durationMs": 34
  },
  "user": {
    "id": 1
  },
  "error": {
    "name": "ErrorName",
    "message": "error message",
    "stack": "stacktrace (prod: opcional/limitado)",
    "code": "INTERNAL_ERROR"
  }
}
```

### O que logar (mínimo)
- **Request start** (opcional em prod, útil em dev/staging)
- **Request end** (recomendado)
  - method, path, status, durationMs, requestId
- **Erros** (sempre)
  - requestId + code + stack (com cuidado em prod)
- **Eventos de domínio relevantes** (info)
  - aula concluída (sem PII excessiva)
  - certificado emitido (sem dados sensíveis)

### Onde logar
- Backend: stdout/stderr (para agregação posterior)
- Frontend: evitar logs ruidosos em prod; usar somente erros críticos

---

## ❗ Erros e exceções

### Padrão de resposta de erro
Todos erros devem seguir o formato definido em `docs/api-spec.md`:

- `code`
- `message`
- `details` (quando validação)
- `requestId`

### Mapeamento recomendado (backend)
- Validação de input → 400 (`VALIDATION_ERROR`)
- Sem auth/token inválido → 401 (`UNAUTHORIZED`)
- Sem permissão → 403 (`FORBIDDEN`)
- Recurso não encontrado → 404 (`NOT_FOUND`)
- Conflito (email já existe, certificado duplicado) → 409 (`CONFLICT`)
- Erro inesperado → 500 (`INTERNAL_ERROR`)

**Regra:** erro inesperado deve:
- registrar log `error` com requestId
- reportar para New Relic
- retornar `INTERNAL_ERROR` sem detalhes sensíveis

---

## 🧪 Healthchecks

### Endpoints recomendados
- `GET /health` → liveness (API está no ar)
- `GET /ready` → readiness (dependências OK: DB)

Resposta exemplo:

```json
{
  "status": "ok",
  "service": "educational-portal-api",
  "version": "git_sha_or_semver",
  "uptimeSeconds": 12345
}
```

Para readiness, incluir dependências:

```json
{
  "status": "ok",
  "dependencies": {
    "database": "ok"
  }
}
```

---

## 📊 Métricas (mínimo viável)

### Métricas de API
- Latência p50/p95/p99 por endpoint
- Taxa de erro (4xx/5xx)
- Throughput (req/min)
- Apdex (se configurado)

### Métricas de domínio (custom)
- Aulas concluídas por dia
- Certificados emitidos por dia
- Taxa de conclusão de cursos (concluídos / iniciados)

**Observação:** evitar métricas que exponham PII.

---

## 🧭 Tracing (New Relic APM)

### Requisitos
- Instrumentação APM habilitada no backend.
- Incluir atributos custom:
  - `requestId`
  - `userId` (quando autenticado)
  - `courseId`, `lessonId` (somente quando relevante)
- Nomear transações por rota (ex.: `GET /api/v1/courses/{id}`)

### Eventos importantes para trace
- Login (sucesso/falha) — com cuidado para não logar credenciais
- Complete lesson (idempotente)
- Emissão de certificado
- Download de certificado

---

## 🔎 Alertas (baseline)

Configurar alertas básicos em staging/prod:

- **High Error Rate**: 5xx acima de X% por Y minutos
- **High Latency**: p95 acima de X ms por Y minutos
- **DB Connectivity**: readiness falhando
- **Crash/Restart loop**: app reiniciando continuamente

(Os thresholds serão definidos após observação de baseline real.)

---

## 🧰 Troubleshooting rápido

### Cenário: usuário não consegue gerar certificado
Verificar:
1. `GET /courses/{courseId}/progress` (completionPercentage = 100?)
2. logs do endpoint de `complete lesson` (requestId)
3. constraints do banco (unicidade de certificate)
4. trace no New Relic do use case de emissão

### Cenário: progresso não atualiza
Verificar:
1. idempotência e retorno do endpoint `complete`
2. índices/constraints de `course_progress`
3. logs de DB errors e `CONFLICT`/`FORBIDDEN`

---

## 🤖 Regras para IA

- Não inventar campos de log fora do padrão sem atualizar este documento.
- Não adicionar PII em logs.
- Sempre incluir `requestId` nos logs e no error payload.
- Se mudar formato de erro, atualizar `docs/api-spec.md` e este documento.
