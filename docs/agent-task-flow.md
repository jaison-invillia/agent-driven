# 🧩 Agent Task Flow

Este documento define **como os agentes de IA devem colaborar** ao longo do ciclo de desenvolvimento do Portal Educacional.

Objetivos:
- padronizar fluxo de trabalho entre agentes
- reduzir retrabalho e conflitos de mudanças
- aumentar previsibilidade e qualidade de entregas
- garantir aderência a arquitetura, domínio, banco e contratos de API

Referências obrigatórias:
- `README.md`
- `docs/ai-context.md`
- `docs/architecture.md`
- `docs/domain.md`
- `docs/database.md`
- `docs/api-spec.md`
- `docs/engineering-guidelines.md`

---

## 👥 Agentes e responsabilidades

### 1) Orchestrator Agent
Responsável por:
- entender o objetivo da tarefa
- decompor em subtarefas
- definir sequência (plano)
- delegar para agentes especializados
- garantir checklist de qualidade (DoD)

Entregas típicas:
- plano de execução (passos + arquivos afetados)
- lista de PRs/commits sugeridos
- validação final (integração do todo)

---

### 2) Product/Backlog Agent
Responsável por:
- refinar requisitos em histórias menores
- definir critérios de aceite
- mapear fluxos principais e exceções
- garantir alinhamento com o escopo

Entregas típicas:
- user stories + acceptance criteria
- casos de uso e edge cases
- definição de pronto (funcional)

---

### 3) Architecture Agent
Responsável por:
- validar aderência a `docs/architecture.md`
- definir estrutura/padrões quando algo novo surgir
- revisar impactos arquiteturais (mudanças grandes)
- sugerir refatorações estruturais

Entregas típicas:
- atualizações em `docs/architecture.md` (se necessário)
- guidelines de implementação por camada
- decisões registradas (ADR opcional)

---

### 4) Database Agent
Responsável por:
- evoluir modelo físico em `docs/database.md`
- criar/validar migrations
- revisar constraints, índices e integridade
- apoiar queries críticas e performance

Entregas típicas:
- migrations
- ajustes em `docs/database.md`
- validação de unicidades (progress/certificates)

---

### 5) Backend Agent
Responsável por:
- implementar use cases (application)
- implementar ports + adapters (infrastructure)
- implementar controllers/DTOs (interfaces)
- garantir conformidade com `docs/api-spec.md`

Entregas típicas:
- código backend por camada
- handlers/controllers e rotas
- testes unitários e de integração

---

### 6) Frontend Agent
Responsável por:
- implementar páginas e componentes (Next.js)
- integrar com API conforme `docs/api-spec.md`
- lidar com estados de loading/erro
- garantir SSR onde aplicável

Entregas típicas:
- páginas `pages/*`
- componentes e hooks
- client API e tratamento de auth

---

### 7) Test Agent
Responsável por:
- garantir cobertura adequada em unit/integration/e2e
- criar testes de edge cases
- validar idempotência e regras de domínio
- sugerir melhorias de testabilidade

Entregas típicas:
- testes unitários de use cases
- testes de integração dos endpoints críticos
- (mínimo) e2e do fluxo feliz

---

### 8) Code Review Agent
Responsável por:
- revisar PRs para padrões e qualidade
- checar Clean Architecture (dependências)
- checar contratos de API e validações
- checar segurança básica e boas práticas

Entregas típicas:
- comentários de PR
- checklist de correções
- sugestão de refatoração incremental

---

### 9) DevOps Agent
Responsável por:
- pipeline CI (lint/test/build)
- configuração de ambientes (dev/staging/prod)
- dockerização (futuro)
- automações de deploy

Entregas típicas:
- workflow GitHub Actions
- scripts de setup local
- configs de env/secrets

---

### 10) Observability Agent
Responsável por:
- instrumentação New Relic (APM/errors)
- padrões de logging e correlação (requestId)
- dashboards/alerts (futuro)
- troubleshooting e métricas

Entregas típicas:
- integração New Relic
- padrões de log estruturado
- documentação de observabilidade

---

### 11) Security Agent
Responsável por:
- revisão de auth (JWT), validações e permissões
- checar OWASP básico (inputs, injection, secrets)
- sugerir hardening (rate limit, headers)
- revisar dependências e configs sensíveis

Entregas típicas:
- checklist de segurança por endpoint
- ajustes de validação/sanitização
- recomendações de política de secrets

---

## 🧠 Regras de ouro do fluxo

1. **Documentação é fonte de verdade**
   - Se uma feature exigir novo endpoint/tabela/entidade → atualizar docs antes.
2. **Mudanças pequenas e incrementais**
   - Preferir PRs menores e revisáveis.
3. **Respeitar camadas**
   - Sem regra de negócio em controller.
   - Sem DB direto fora de repositories/adapters.
4. **Idempotência e unicidade**
   - Especialmente em progress e certificate.
5. **Testes acompanham mudanças**
   - Use cases devem ter unit tests; endpoints críticos, integration.

---

## 🔁 Fluxos principais

### A) Iniciar um novo projeto (bootstrap)

1. **Orchestrator**: define plano + estrutura do repo
2. **Product**: define backlog inicial (MVP) + critérios de aceite
3. **Architecture**: define style (Clean Arch) + estrutura de pastas
4. **Database**: define schema inicial + migrations
5. **API** (via Backend/Architecture): consolida `docs/api-spec.md`
6. **DevOps**: cria CI básico (lint/test/build)
7. **Observability**: define padrão de requestId + integração inicial
8. **Security**: define baseline de auth e validações

Saída: repositório pronto para iniciar features com padrão consistente.

---

### B) Criar uma nova feature (ex.: “marcar aula como concluída”)

1. **Orchestrator**: cria plano (arquivos e passos)
2. **Product**: escreve user story + critérios de aceite + edge cases
3. **Architecture**: valida que a feature cabe sem quebra (ou propõe ajuste)
4. **Database**: valida constraints/índices (se houver impacto)
5. **Backend**:
   - adiciona/ajusta use case (Application)
   - implementa adapter/port (Infrastructure)
   - cria controller/DTO/route (Interfaces)
6. **Frontend**:
   - integra endpoint
   - UI/UX (loading/erro)
7. **Test Agent**:
   - unit test do use case
   - integration test do endpoint
8. **Code Review**: valida padrões + contracts + clean arch
9. **Observability/Security**: valida logs/correlação e hardening mínimo

---

### C) Detectar e resolver bugs

1. **Orchestrator**: reproduz/define hipótese + plano de investigação
2. **Observability**: usa logs/APM para localizar origem
3. **Backend/Frontend**: corrige com menor mudança possível
4. **Test Agent**: cria teste de regressão
5. **Code Review**: valida que não houve violação de arquitetura
6. **Security**: se bug for relacionado a auth/inputs, reforça validação

---

## ✅ Definition of Done (DoD) para tarefas com agentes

Uma tarefa está concluída quando:
- [ ] docs atualizados (se houve mudança de contrato/domínio/banco)
- [ ] implementação respeita `docs/architecture.md`
- [ ] API aderente a `docs/api-spec.md`
- [ ] testes adicionados/atualizados
- [ ] observabilidade mínima (requestId + logs/erros)
- [ ] revisão de qualidade concluída
