# 🔄 Guia de Replicação

Guia passo a passo para replicar a estrutura de agentes de IA em outro repositório.

---

## Pré-requisitos

- VS Code com extensão GitHub Copilot (com suporte a agentes)
- Acesso ao MCP do GitHub (já configurado no Copilot)
- Repositório Git inicializado

---

## Passo 1 — Copiar a estrutura de arquivos

Copie os seguintes diretórios e arquivos para o novo repositório:

```
.github/
  agents/            # Copiar todos os .agent.md
  prompts/           # Copiar todos os .prompt.md
  instructions/      # Copiar todos os .instructions.md
  skills/            # Copiar todas as pastas de skills
  copilot-instructions.md

.vscode/
  mcp.json           # Configuração de MCP servers

AGENTS.md            # Definição da squad
```

---

## Passo 2 — Configurar MCP

### GitHub MCP (obrigatório)
O MCP do GitHub já vem configurado com o Copilot. Verifique `.vscode/mcp.json`:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

### MCPs adicionais (opcional)
Para adicionar Figma, Playwright ou outros MCPs, adicione entradas ao `mcp.json`:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "playwright": {
      "type": "http",
      "url": "<playwright-mcp-url>"
    }
  }
}
```

---

## Passo 3 — Adaptar a documentação

### Documentos obrigatórios
Crie os seguintes documentos adaptados ao seu projeto (veja `docs/engineering-docs-recommendation.md` para templates):

| Documento | Obrigatório? | Propósito |
|-----------|-------------|-----------|
| `docs/architecture.md` | ✅ | Estilo arquitetural e camadas |
| `docs/domain.md` | ✅ | Entidades e regras de negócio |
| `docs/api-spec.md` | ✅ (se tem API) | Contratos HTTP |
| `docs/database.md` | ✅ (se tem DB) | Schema e constraints |
| `docs/security.md` | ✅ | Baseline de segurança |
| `docs/engineer-guidelines.md` | ✅ | Padrões de código e testes |
| `docs/observability.md` | Recomendado | Logging e monitoramento |
| `docs/project-structure.md` | Recomendado | Layout de pastas |
| `docs/ai/ai-context.md` | Recomendado | Contexto condensado para IA |
| `CONTEXT_PACK.md` | Recomendado | Snapshot para onboarding rápido |

### Atualizar referências nos agentes
Os agentes fazem referência a paths de documentação. Se seus docs estiverem em local diferente, atualize:
- Seção "Mandatory documentation to read" em cada `.agent.md`
- Referências em `copilot-instructions.md`
- Referências em `AGENTS.md`

---

## Passo 4 — Customizar os agentes

### Ajustes mínimos necessários

1. **Product Owner**: Adaptar se usar Jira em vez de GitHub Issues
2. **Architect**: Adaptar referências de ADRs e estilo arquitetural
3. **Backend/Frontend**: Adaptar para stack tecnológica do projeto
4. **Test Advisor**: Adaptar para framework de testes do projeto

### Ajustes nas Instructions

Adaptar os `applyTo` patterns nos `.instructions.md` para a estrutura de pastas do novo projeto:

```yaml
# Exemplo: se o backend está em /server ao invés de /backend
applyTo: "server/src/**/*.ts"
```

---

## Passo 5 — Adaptar os Instructions

Revise cada `.instructions.md` e ajuste:
- Patterns de `applyTo` para a estrutura do novo projeto
- Convenções de naming se diferentes
- Regras específicas da stack (ex: Python em vez de TypeScript)

---

## Passo 6 — Testar

### Teste rápido de cada agente
1. Abra o VS Code no novo repositório
2. No Copilot Chat, invoque cada agente e verifique se responde conforme o papel
3. Teste cada slash command (`/new-feature`, `/analyze-issue`, etc.)

### Teste de fluxo end-to-end
1. `/new-feature` → PO deve criar uma issue
2. `/analyze-issue #N` → Architect deve postar análise
3. `/implement-issue #N` → Staff deve planejar e delegar
4. `/review-pr #N` → Reviewer deve revisar
5. `/document-pr #N` → Documenter deve atualizar docs

---

## Passo 7 — Documentar

Crie ou atualize o `AGENTS.md` do novo repositório com:
- Lista dos agentes ativos
- Fluxos de trabalho específicos do projeto
- Customizações feitas na replicação

---

## Checklist de replicação

- [ ] Estrutura `.github/` copiada
- [ ] MCP configurado em `.vscode/mcp.json`
- [ ] Documentação base criada (`docs/`)
- [ ] Referências nos agentes atualizadas para paths locais
- [ ] Instructions adaptados para estrutura do projeto
- [ ] `AGENTS.md` atualizado
- [ ] `copilot-instructions.md` adaptado
- [ ] Teste de cada agente realizado
- [ ] Fluxo end-to-end testado

---

## Dicas

- **Comece simples**: Use apenas PO + Architect + Staff + BE/FE + Reviewer no início
- **Adicione gradualmente**: QA, Documenter e Metrifier podem ser adicionados depois
- **Documente primeiro**: A qualidade dos agentes depende da qualidade da documentação
- **Itere**: Ajuste as instruções dos agentes conforme aprende o que funciona melhor
