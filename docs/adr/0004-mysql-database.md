# ADR-0004: Use MySQL 8 as the Primary Database

## Status
Accepted

## Context
O Portal Educacional necessita de um banco de dados para armazenar:

- usuários
- cursos
- aulas
- progresso dos alunos
- certificados emitidos

Algumas alternativas foram consideradas.

### 1. PostgreSQL
- Muito robusto e poderoso
- Excelente suporte a recursos avançados
- Amplamente utilizado em sistemas modernos

### 2. MySQL / MariaDB
- Muito difundido no mercado
- Excelente suporte em provedores de cloud
- Fácil operação e administração
- Bom desempenho para workloads transacionais

### 3. NoSQL (MongoDB, etc)
- Mais flexível para dados não estruturados
- Porém o domínio deste sistema é altamente relacional:
  - usuários → cursos → aulas → progresso → certificados

Portanto, um **banco relacional** é mais adequado.

## Decision

O sistema utilizará **MySQL 8** como banco de dados principal.

### Motivações

- Modelo de dados claramente relacional
- Simplicidade operacional
- Alta compatibilidade com ferramentas Node.js
- Suporte amplo em cloud providers
- Facilidade para migrations e integridade referencial

### Características esperadas

- Uso de **InnoDB**
- Suporte a **foreign keys**
- Constraints de unicidade para regras de domínio
- Índices apropriados para consultas de progresso e cursos

### Modelagem

Principais tabelas:

- `users`
- `courses`
- `lessons`
- `course_progress`
- `certificates`

Definições completas do schema estão em:

- `docs/database.md`

### Migrations

Mudanças no schema devem ocorrer **exclusivamente via migrations versionadas**.

Regras:

- Nunca alterar schema manualmente em produção.
- Toda migration deve ser reversível quando possível.
- Migrations devem acompanhar alterações em `docs/database.md`.

## Consequences

### Positive

- Banco relacional adequado ao domínio.
- Integridade referencial garantida por constraints.
- Simplicidade de operação e manutenção.
- Ampla compatibilidade com ORMs e drivers Node.js.

### Negative

- Menor flexibilidade que bancos NoSQL para dados semi-estruturados.
- Escala horizontal pode exigir soluções adicionais no futuro.

### Mitigations

- Uso adequado de índices.
- Possibilidade futura de cache (Redis) para leituras frequentes.
- Possibilidade de replicação ou sharding se necessário.

## Notes

Esta decisão impacta:

- Modelagem do domínio (`docs/domain.md`)
- Schema do banco (`docs/database.md`)
- Repositórios de infraestrutura (`backend/src/infrastructure/repositories`)

Mudanças futuras no banco devem ser registradas em novas ADRs se alterarem significativamente a arquitetura.
