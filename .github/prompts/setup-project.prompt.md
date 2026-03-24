---
description: "Configure the project stack and update all template placeholders interactively."
mode: agent
agent: project-setup
---

# Setup Project

Configure este projeto definindo a stack de tecnologias, padrões e convenções.

O agente `project-setup` irá:

1. **Coletar informações** sobre o projeto: linguagem, framework, banco de dados, ferramentas de observabilidade, etc.
2. **Confirmar as escolhas** antes de aplicar mudanças.
3. **Atualizar automaticamente** toda a documentação e configuração do template (CONTEXT_PACK.md, docs/, .github/instructions/, etc.).
4. **Sugerir ADRs** para registrar as decisões técnicas tomadas.

> **Dica:** Este é o primeiro passo recomendado após clonar o template.
