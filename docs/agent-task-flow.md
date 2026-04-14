# 🧩 Agent Task Flow

Este documento define **como os agentes de IA devem colaborar** ao longo do ciclo de desenvolvimento.

Objetivos:
- Padronizar fluxo de trabalho entre agentes
- Reduzir retrabalho e conflitos de mudanças
- Aumentar previsibilidade e qualidade de entregas
- Garantir aderência à arquitetura, domínio, banco e contratos de API

Referências obrigatórias:
- `README.md`
- `docs/ai/ai-context.md`
- `docs/architecture.md`
- `docs/domain.md`
- `docs/database.md`
- `docs/api-spec.md`
- `docs/engineer-guidelines.md`

---

## 👥 Agentes e responsabilidades

### 1) Product Owner (`product-owner`)
Converte demandas de negócio em issues claras e acionáveis.

Responsabilidades:
- Esclarecer demandas ambíguas (gate bloqueante)
- Definir critérios de aceite (Given/When/Then)
- Atribuir prioridade (P0–P3)
- Criar/atualizar Issues no GitHub via MCP com checklist de subtarefas
- Validar soluções implementadas contra critérios de aceite

Entregas: Issue criada com contexto, critérios, prioridade e subtarefas.

---

### 2) Architect (`architect`)
Analisa issues sob perspectiva arquitetural.

Responsabilidades:
- Identificar camadas arquiteturais afetadas (conforme `docs/architecture.md`)
- Sugerir estrutura de arquivos/módulos por camada
- Verificar conformidade com fronteiras arquiteturais
- Propor ADRs quando necessário
- Postar análise arquitetural como comentário no issue via MCP

Entregas: Análise arquitetural como comment no issue.

---

### 3) Staff / Orchestrator (`staff`)
Orquestrador central que planeja e coordena a implementação.

Responsabilidades:
- Ler contexto do PO e análise do Architect
- Esclarecer ambiguidades e validar qualidade da descrição da tarefa antes de iniciar
- Acionar `documenter` no início de toda tarefa para mini-plano documental obrigatório
- Planejar implementação a nível de código (arquivos, ordem, dependências)
- Documentar plano no issue via MCP
- Classificar a tarefa como `feature_nova` ou `mudanca_existente`
- Consultar `test-advisor` para estratégia de testes por classificação
- Delegar para `backend-dev` e/ou `frontend-dev` (sub-agentes)
- Paralelizar delegações sempre que houver independência de dependências
- Validar resultados e rodar testes
- Consultar `metrifier` para recomendações de observabilidade
- Acionar `reviewer` antes da finalização somente quando houver mudança de código
- Criar branch e abrir PR via MCP

Delega para: `backend-dev`, `frontend-dev`, `test-advisor`, `qa`, `metrifier`, `reviewer`, `documenter`

Entregas: Plano documentado no issue + PR aberta.

---

### 4) Backend Developer (`backend-dev`)
Implementa código backend seguindo o estilo arquitetural definido em `docs/architecture.md`. **Sub-agente** (invocado pelo Staff).

Responsabilidades:
- Implementar entidades de domínio, use cases, ports
- Implementar repositórios, controllers, DTOs, rotas
- Conectar dependências no composition root
- Escrever testes unitários e de integração

---

### 5) Frontend Developer (`frontend-dev`)
Implementa código frontend usando o framework definido em `CONTEXT_PACK.md`. **Sub-agente** (invocado pelo Staff).

Responsabilidades:
- Implementar páginas seguindo os patterns do framework frontend
- Criar componentes, hooks/equivalentes, services
- Integrar com API conforme `docs/api-spec.md`
- Tratar estados de UI (loading, error, empty, token expirado)

---

### 6) Test Advisor (`test-advisor`)
Propõe estratégias de teste sem escrever código.

Responsabilidades:
- Analisar tarefas para identificar cenários testáveis
- Considerar classificação `feature_nova` vs `mudanca_existente` fornecida pelo Staff
- Em `feature_nova`, priorizar novos testes para novas capacidades
- Em `mudanca_existente`, priorizar ajuste de testes existentes quando cobertura já for suficiente
- Propor testes por nível da pirâmide (unit → integration → e2e)
- Definir estratégias de mock e fixtures
- Identificar edge cases e cenários de segurança

---

### 7) QA (`qa`)
Valida implementações executando testes e verificando critérios de aceite.

Responsabilidades:
- Executar suítes de testes automatizados
- Validar critérios de aceite do issue
- Testar edge cases e cenários de segurança
- Postar relatório de QA no issue via MCP

**Futuro**: Integração com Playwright MCP para testes em browser.

---

### 8) Reviewer (`reviewer`)
Revisa Pull Requests contra guidelines do projeto.

Responsabilidades:
- Revisar código para conformidade com `docs/architecture.md`
- Validar aderência a contratos de API
- Verificar práticas de segurança e observabilidade
- Verificar existência e cobertura de testes
- Postar review na PR via MCP

Regra de acionamento:
- Obrigatório apenas quando houver mudança de código
- Em tarefas apenas documentais, registrar review como não aplicável no issue

---

### 9) Documenter (`documenter`)
Avalia e atualiza documentação desde o início da tarefa até o fechamento.

Responsabilidades:
- No início de toda tarefa, produzir mini-plano documental obrigatório (`required`, `optional`, `none`)
- Indicar docs candidatas para atualização e justificativa
- Analisar diff da PR para identificar mudanças documentáveis
- Atualizar docs afetados (api-spec, database, domain, architecture, etc.)
- Criar ADRs para decisões arquiteturais
- Atualizar CONTEXT_PACK.md para mudanças significativas

---

### 10) Metrifier (`metrifier`)
Sugere métricas e instrumentação de observabilidade.

Responsabilidades:
- Propor métricas de negócio, técnicas e operacionais
- Recomendar métodos de coleta (logs, APM, custom metrics)
- Sugerir thresholds de alerta e estrutura de dashboards
- Aconselhar sobre SLI/SLO

---

### 11) Pathfinder (`pathfinder`)
Consultor de fluxo que diagnostica tarefas incertas e sugere o roteiro ideal de agentes.

Responsabilidades:
- Realizar perguntas de diagnóstico para entender natureza, escopo e incerteza da tarefa
- Ler cards/issues de qualquer tracker configurado para coletar contexto
- Mapear tarefas para os agentes apropriados e sua ordem ideal de execução
- Sugerir roteiro de desenvolvimento de alto nível inspirado em fluxos ágeis
- Identificar riscos, dependências e ambiguidades antes da execução
- Explicar por que cada agente deve (ou não) ser envolvido

Entregas: Roteiro sugerido com sequência de agentes, justificativas, riscos e como iniciar.

---

## 🔄 Modelo de delegação

```
Usuário
 │
 ├── pathfinder ──→ Sugere fluxo de agentes (ponto de entrada opcional)
 │
 ├── product-owner ──→ Cria/atualiza Issue
 │
 ├── architect ──→ Posta análise arquitetural no Issue
 │
 ├── staff (orchestrator)
 │     ├── backend-dev ──→ Implementa backend
 │     │     └── test-advisor (consulta)
 │     ├── frontend-dev ──→ Implementa frontend
 │     │     └── test-advisor (consulta)
 │     ├── test-advisor ──→ Propõe estratégia de testes
 │     ├── metrifier ──→ Sugere métricas
 │     ├── reviewer ──→ Revisa apenas com mudança de código
 │     ├── documenter ──→ Mini-plano documental no início
 │     └── qa ──→ Valida implementação
 │
 ├── reviewer ──→ Revisa PR quando houver mudança de código
 │
 └── documenter ──→ Mini-plano inicial + atualização final
```

---

## 📋 Protocolo de Issue Tracking

Todos os agentes com acesso ao GitHub MCP devem manter o card atualizado:

1. **Ao iniciar**: Comentar indicando que o agente está atuando
2. **Progresso**: Atualizar com o que foi feito e o que falta
3. **Subtarefas**: Usar checklists (`- [ ]`) para rastrear progresso
4. **Sub-issues**: Criar issues vinculadas para tarefas grandes
5. **Ao finalizar**: Postar resumo com links para PR e status das subtarefas

Formato padrão:
```markdown
## 🤖 [Nome do Agente] — Status Update
**Status**: 🟡 Em progresso / ✅ Concluído / 🔴 Bloqueado

### Subtarefas
- [x] Tarefa concluída
- [ ] Tarefa pendente

### Notas
[Detalhes, decisões, blockers]
```

---

## 🧠 Regras de ouro

1. **Documentação é fonte de verdade**
   - Se uma feature exigir novo endpoint/tabela/entidade → atualizar docs antes.
2. **Mudanças pequenas e incrementais**
   - Preferir PRs menores e revisáveis.
3. **Respeitar camadas**
   - Sem regra de negócio em controller.
   - Sem DB direto fora de repositories/adapters.
4. **Idempotência e unicidade**
   - Conforme definido em `docs/domain.md` e `docs/database.md`.
5. **Testes acompanham mudanças**
   - Use cases devem ter unit tests; endpoints críticos, integration.

---

## 🔁 Fluxos principais

> **Dica:** Quando a tarefa é incerta ou você não sabe por onde começar, use o `pathfinder` primeiro (`/plan-task`) para receber um roteiro recomendado antes de entrar em qualquer fluxo abaixo.

### A) Nova feature
```
(pathfinder) → product-owner → architect → staff(+documenter-start) → [backend-dev, frontend-dev] → qa → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(opcional)*: Diagnostica a tarefa e sugere o fluxo ideal de agentes
1. **Product Owner**: Esclarece demanda, cria issue com critérios e subtarefas
2. **Architect**: Analisa impacto arquitetural, posta análise no issue
3. **Staff**: Valida ambiguidades, aciona documenter (mini-plano), classifica testes, consulta test-advisor e delega para BE/FE em paralelo quando possível
4. **Backend/Frontend**: Implementam código e testes
5. **QA**: Executa testes, valida critérios de aceite
6. **Reviewer**: Revisa PR contra guidelines quando houver mudança de código
7. **Documenter**: Revalida impacto e atualiza documentação no fechamento

### B) Bug fix
```
(pathfinder) → product-owner (clarify) → staff(+documenter-start) → [backend-dev/frontend-dev] → qa → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(opcional)*: Diagnostica o bug e sugere o fluxo de correção
1. **Product Owner**: Esclarece bug report, define critérios de correção
2. **Staff**: Investiga root cause, valida ambiguidades, aciona documenter (mini-plano), classifica como `mudanca_existente`, planeja fix mínimo e delega
3. **Backend/Frontend**: Corrige com menor mudança possível + ajuste de testes existentes (ou novos testes quando houver gaps)
4. **QA**: Valida que o bug foi corrigido e não há regressão
5. **Reviewer**: Verifica qualidade da correção quando houver mudança de código
6. **Documenter**: Atualiza se houve mudança de contrato

### C) Novo projeto (bootstrap)
```
(pathfinder) → product-owner (backlog) → architect (estrutura) → staff(+documenter-start, scaffold) → documenter(final)
```

0. **Pathfinder** *(opcional)*: Diagnostica o escopo do projeto e sugere o fluxo de bootstrap
1. **Product Owner**: Define backlog inicial (MVP)
2. **Architect**: Define estilo arquitetural, estrutura de pastas, ADRs iniciais
3. **Staff**: Cria scaffold do projeto
4. **Documenter**: Documenta estrutura e decisões

### D) Manutenção / tech debt
```
(pathfinder) → architect → staff(+documenter-start) → [backend-dev/frontend-dev] → reviewer(code-change) → documenter(final)
```

0. **Pathfinder** *(opcional)*: Diagnostica o impacto e sugere a melhor abordagem
1. **Architect**: Avalia impacto arquitetural da mudança
2. **Staff**: Planeja e delega implementação
3. **Backend/Frontend**: Implementam refatoração
4. **Reviewer**: Valida que não houve violação de arquitetura
5. **Documenter**: Atualiza documentação afetada

---

## ✅ Definition of Done (DoD)

Uma tarefa está concluída quando:
- [ ] Mini-plano documental registrado no início (`required`/`optional`/`none`)
- [ ] Docs atualizados (se houve mudança de contrato/domínio/banco)
- [ ] Implementação respeita `docs/architecture.md`
- [ ] API aderente a `docs/api-spec.md`
- [ ] Estratégia de testes definida (`feature_nova`/`mudanca_existente`)
- [ ] Testes adicionados/ajustados conforme estratégia
- [ ] Observabilidade mínima (requestId + logs/erros)
- [ ] Revisão de qualidade concluída quando houver mudança de código
- [ ] Issue card atualizado com status final
- [ ] PR vinculada ao issue
