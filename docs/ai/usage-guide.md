# Guia de Uso da Squad de Agentes

Guia prático de como usar os agentes de IA no dia a dia. Cada cenário mostra **exatamente o que digitar** no Copilot Chat do VS Code e o que esperar de cada passo.

> **Pré-requisito**: VS Code com GitHub Copilot ativo e MCP do GitHub configurado em `.vscode/mcp.json`.

---

## Resumo rápido

| Comando | O que faz | Agente |
|---------|-----------|--------|
| `/new-feature` | Cria issue a partir de uma demanda | Product Owner |
| `/analyze-issue` | Análise arquitetural de um issue | Architect |
| `/implement-issue` | Planeja e implementa um issue | Staff |
| `/fix-bug` | Investiga e corrige um bug | Staff |
| `/review-pr` | Revisa uma Pull Request | Reviewer |
| `/document-pr` | Atualiza docs após merge | Documenter |

Além dos comandos, você pode invocar agentes diretamente digitando `@nome-do-agente` no chat:
`@product-owner`, `@architect`, `@staff`, `@reviewer`, `@documenter`, `@test-advisor`, `@qa`, `@metrifier`

---

## Cenário 1 — Iniciar um novo projeto

Use este fluxo quando for criar um projeto do zero com a estrutura de agentes.

### Passo 1: Definir o backlog inicial

```
/new-feature

Preciso criar um portal educacional onde alunos se cadastram,
acessam cursos, assistem aulas no YouTube, marcam progresso por aula
e recebem certificado ao completar o curso.
Crie issues para as funcionalidades principais do MVP.
```

O **Product Owner** vai:
- Fazer perguntas de esclarecimento (se necessário)
- Criar issues no GitHub com critérios de aceite e subtarefas
- Atribuir prioridades (P0–P3)

### Passo 2: Definir a arquitetura

```
/analyze-issue #1

Analise este issue e defina a arquitetura do projeto,
incluindo estilo arquitetural, estrutura de pastas e ADRs iniciais.
```

O **Architect** vai:
- Definir camadas e dependências
- Sugerir estrutura de pastas
- Propor ADRs (ex: Clean Architecture, JWT, MySQL)
- Postar a análise como comentário no issue

### Passo 3: Gerar o scaffold

```
/implement-issue #1

Crie a estrutura inicial do projeto (scaffold) com base
na análise arquitetural.
```

O **Staff** vai:
- Ler a análise do Architect no issue
- Planejar a estrutura de arquivos
- Delegar para `backend-dev` (scaffold backend) e `frontend-dev` (scaffold frontend)
- Criar branch e abrir PR

### Passo 4: Documentar

```
/document-pr #1

Documente a estrutura criada e as decisões tomadas.
```

O **Documenter** vai:
- Analisar o diff da PR
- Criar/atualizar docs relevantes (architecture.md, project-structure.md, ADRs)
- Atualizar CONTEXT_PACK.md

---

## Cenário 2 — Criar e implementar uma feature

Fluxo completo: demanda → issue → análise → código → testes → review → docs.

### Passo 1: Criar o issue

```
/new-feature

Usuários precisam poder marcar aulas como concluídas.
O progresso deve ser por aula, idempotente, e vinculado ao usuário logado.
Ao completar todas as aulas de um curso, o progresso do curso deve ir para 100%.
```

O **Product Owner** cria o issue com:
- Contexto da demanda
- Critérios de aceite (Given/When/Then)
- Prioridade
- Checklist de subtarefas

### Passo 2: Análise arquitetural

```
/analyze-issue #42
```

O **Architect** posta no issue:
- Camadas afetadas (domain, application, infrastructure, interfaces)
- Arquivos a criar/modificar por camada
- Necessidade de ADR (se houver)
- Riscos e pontos de atenção

### Passo 3: Implementar

```
/implement-issue #42
```

O **Staff** orquestra tudo automaticamente:
1. Lê o issue (contexto do PO + análise do Architect)
2. Lê docs obrigatórios (domain, api-spec, database, architecture)
3. Monta plano de implementação e posta no issue
4. Consulta `@test-advisor` para estratégia de testes
5. Delega para `@backend-dev` (use case, repository, controller, testes)
6. Delega para `@frontend-dev` (se tiver componente de UI)
7. Roda testes para validar
8. Consulta `@metrifier` para observabilidade
9. Cria branch e abre PR

> **Dica**: O Staff atualiza o issue com progresso em cada etapa.

### Passo 4: Review

```
/review-pr #15
```

O **Reviewer** verifica:
- Clean Architecture (sem violação de camadas)
- Aderência a `api-spec.md` e `database.md`
- Segurança (validação, dados sensíveis)
- Observabilidade (requestId, logs)
- Existência de testes

### Passo 5: Documentar

```
/document-pr #15
```

O **Documenter** atualiza docs caso a feature tenha introduzido:
- Novo endpoint → atualiza `api-spec.md`
- Nova tabela/coluna → atualiza `database.md`
- Nova entidade → atualiza `domain.md`
- Decisão arquitetural → cria ADR

### Atalho: Ciclo completo em um comando

Se preferir rodar o ciclo inteiro de uma vez, use a skill `full-feature-cycle`:

```
Preciso de uma feature para marcar aulas como concluídas.
O progresso deve ser por aula, idempotente, e vinculado ao usuário logado.
```

Ao detectar que se trata de uma feature completa, o Copilot pode acionar a skill que executa PO → Architect → Staff → QA → Reviewer → Documenter sequencialmente.

---

## Cenário 3 — Corrigir um bug

### Opção A: Bug já tem issue

```
/fix-bug #55

O endpoint POST /courses/1/lessons/3/complete está retornando 500
quando o usuário tenta completar uma aula que já foi completada.
Deveria retornar 200 (idempotente).
```

### Opção B: Bug ainda não tem issue

```
/fix-bug

O certificado não está sendo gerado quando o aluno completa
a última aula do curso. O progresso mostra 100% mas o
endpoint GET /courses/1/certificate retorna 404.
```

Em ambos os casos, o **Staff**:
1. Se não houver issue, cria um com o contexto do bug
2. Investiga a root cause (lê código, testa hipóteses)
3. Planeja o fix mínimo necessário
4. Delega para `@backend-dev` ou `@frontend-dev`
5. Garante que um teste de regressão é adicionado
6. Abre PR com a correção

Depois, siga o mesmo fluxo de review e documentação:

```
/review-pr #16
/document-pr #16
```

---

## Cenário 4 — Refatoração e tech debt

Para mudanças técnicas que não vêm de demanda de negócio.

### Passo 1: Avaliar impacto arquitetural

```
@architect

Preciso extrair a lógica de validação de progresso que está
duplicada nos use cases CompleteLesson e GetCourseProgress
para um domain service compartilhado. Analise o impacto.
```

O **Architect** avalia:
- Quais camadas e arquivos são afetados
- Se a mudança respeita as fronteiras de Clean Architecture
- Se é necessário ADR

### Passo 2: Implementar

```
@staff

Implemente a refatoração sugerida pelo Architect,
extraindo a validação de progresso para um domain service.
```

### Passo 3: Review e docs

```
/review-pr #17
/document-pr #17
```

---

## Cenário 5 — Consultar estratégia de testes

Quando você quer saber **que testes escrever** antes de implementar.

```
@test-advisor

Vou implementar o endpoint POST /courses/{courseId}/lessons/{lessonId}/complete.
Quais testes devo escrever? Considere unit, integration e edge cases.
```

O **Test Advisor** retorna:
- Testes unitários (use case com mocks de ports)
- Testes de integração (repository + banco, endpoint HTTP)
- Edge cases (aula já completada, curso inexistente, usuário sem permissão)
- Estratégia de mocking
- Fixtures necessárias

> **Nota**: O Test Advisor **propõe** testes mas não escreve código. Quem implementa é o `backend-dev` ou `frontend-dev`.

---

## Cenário 6 — Solicitar métricas e observabilidade

```
@metrifier

Vamos lançar o módulo de certificados. Quais métricas e
instrumentações devemos adicionar?
```

O **Metrifier** sugere:
- Métricas de negócio (certificados gerados por dia, taxa de conclusão)
- Métricas técnicas (latência do endpoint, taxa de erros)
- Métricas operacionais (uso de memória na geração de PDF)
- Alertas recomendados
- Estrutura de dashboard

---

## Cenário 7 — Validar uma implementação (QA)

Depois que o código foi implementado mas antes do merge:

```
@qa #42

Valide a implementação do issue #42 (completar aula).
Execute os testes e verifique os critérios de aceite.
```

O **QA**:
- Roda a suíte de testes automatizados
- Valida cada critério de aceite do issue
- Testa edge cases (inputs inválidos, dados duplicados)
- Posta relatório estruturado no issue

---

## Cenário 8 — Triagem completa de demanda

Quando uma demanda nova chega e precisa passar por todo o pipeline de análise antes da implementação.

```
Preciso que alunos possam avaliar cursos com uma nota de 1 a 5 estrelas,
com comentário opcional. A média deve aparecer na listagem de cursos.
```

A skill `issue-triage` orquestra automaticamente:
1. **Product Owner**: Esclarece, cria issue com critérios de aceite
2. **Architect**: Analisa impacto arquitetural (nova entidade? nova tabela? novos endpoints?)
3. **Staff**: Monta plano de implementação e documenta no issue

O resultado é um issue **pronto para implementar** com:
- Contexto de negócio
- Critérios de aceite
- Análise arquitetural
- Plano de implementação

Depois, basta rodar:
```
/implement-issue #N
```

---

## Dicas de uso

### Quando usar cada abordagem

| Situação | O que fazer |
|----------|-------------|
| Demanda vaga, precisa de esclarecimento | `/new-feature` (PO faz perguntas) |
| Issue já existe, quer implementar | `/implement-issue #N` |
| Bug reportado pelo usuário | `/fix-bug` com descrição |
| Refatoração técnica | `@architect` para analisar + `@staff` |
| Dúvida sobre testes | `@test-advisor` |
| PR pronta para review | `/review-pr #N` |
| PR mergeada, precisa documentar | `/document-pr #N` |
| Quer métricas/alertas | `@metrifier` |
| Quer validar antes do merge | `@qa #N` |

### Boas práticas

1. **Sempre comece pelo PO** para features novas — garante issue bem definido
2. **Sempre passe pelo Architect** antes de implementar — evita retrabalho
3. **Deixe o Staff orquestrar** — ele sabe a ordem das dependências
4. **Não pule o review** — o Reviewer valida Clean Architecture, segurança e testes
5. **Documente após merge** — o Documenter garante que os docs ficam atualizados
6. **Consulte o Test Advisor cedo** — saber os testes antes ajuda a implementar melhor

### O que NÃO fazer

- Não invoque `@backend-dev` ou `@frontend-dev` diretamente — eles são sub-agentes do Staff
- Não pule a documentação — docs desatualizados degradam a qualidade dos agentes
- Não ignore as perguntas do PO — elas garantem que o issue será claro
- Não implemente sem issue — toda mudança deve ser rastreável

---

## Referências

- [AGENTS.md](../../../AGENTS.md) — Definição completa dos agentes
- [docs/agent-task-flow.md](../agent-task-flow.md) — Fluxos detalhados e regras
- [docs/ai/agent-squad-guide.md](agent-squad-guide.md) — Metodologia e customização
- [docs/ai/replication-guide.md](replication-guide.md) — Como replicar em outro repo
