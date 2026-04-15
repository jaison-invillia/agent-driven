---
description: "Configure the project stack, tooling integrations, and update all template placeholders interactively."
mode: agent
agent: project-setup
---

# Setup Project

Configure this project by defining the technology stack, tools, and conventions.

The `project-setup` agent will:

1. **Detect the environment** automatically: check MCP servers, existing project files, and already-filled placeholders (Phase 0).
2. **Collect information** about the project: language, framework, database, observability tools, issue tracker, MCP servers, etc.
3. **Confirm choices** before applying changes.
4. **Automatically update** all template documentation and configuration (README.md, CONTEXT_PACK.md, docs/, .github/instructions/, .vscode/mcp.json, etc.).
5. **Suggest ADRs** to record the technical decisions made.
6. **Validate** that all stack placeholders have been filled.
7. **Generate Copilot Productivity Readiness checklist** — showing what is ready and what needs action.

> **Tip:** This is the recommended first step after cloning the template.
> If already run before, the agent detects what was configured and offers selective updates.
