# ADR-0006: Use Next.js with Server Side Rendering (SSR) for the Frontend

## Status
Accepted

## Context
O Portal Educacional possui um frontend responsável por:

- autenticação de usuários
- listagem de cursos
- exibição de aulas
- acompanhamento de progresso
- acesso a certificados

O frontend precisa consumir a API backend e fornecer uma experiência rápida e simples para os alunos.

Alternativas consideradas:

### 1. React SPA (Single Page Application)
- Renderização totalmente no cliente
- Simples de implementar
- Porém pode ter:
  - pior performance inicial
  - SEO limitado
  - necessidade maior de gerenciamento de estado e carregamento

### 2. Next.js com SSR (Server Side Rendering)
- Renderização inicial no servidor
- Melhor performance inicial
- Melhor SEO
- Estrutura de projeto bem definida
- Suporte nativo a rotas e otimizações

### 3. Next.js com SSG (Static Site Generation)
- Excelente para conteúdo estático
- Porém cursos e progresso são dinâmicos
- Nem todas as páginas podem ser geradas estaticamente

Como o sistema possui conteúdo parcialmente dinâmico (ex.: progresso do usuário) e precisa de boa experiência inicial, SSR é uma opção equilibrada.

## Decision

O frontend será implementado utilizando **Next.js com Server Side Rendering (SSR)**.

### Estrutura básica do frontend

O projeto seguirá a estrutura padrão do Next.js:

```
frontend/
  pages/
  components/
  hooks/
  services/
  styles/
```

### Renderização

Tipos de renderização esperados:

- **SSR**
  - páginas de cursos
  - detalhes de curso
- **Client-side rendering**
  - progresso do usuário
  - interações dinâmicas
- **Static assets**
  - páginas institucionais (se houver no futuro)

### Integração com API

O frontend consumirá a API backend via HTTP.

Base da API:

```
/api/v1
```

Autenticação será feita usando:

```
Authorization: Bearer <token>
```

Conforme definido em:

- `docs/api-spec.md`
- `docs/security.md`

## Consequences

### Positive

- Melhor performance inicial de páginas
- Estrutura organizada e padronizada
- Melhor suporte a SEO
- Ecossistema forte e amplamente adotado
- Boa integração com React

### Negative

- Maior complexidade que uma SPA simples
- Necessidade de entender diferentes estratégias de renderização

### Mitigations

- Começar com SSR apenas onde necessário
- Manter componentes desacoplados
- Utilizar hooks e services para separar lógica de API

## Notes

Esta decisão impacta:

- arquitetura do frontend
- estratégia de renderização de páginas
- integração com a API

Detalhes adicionais sobre arquitetura estão em:

- `docs/architecture.md`
