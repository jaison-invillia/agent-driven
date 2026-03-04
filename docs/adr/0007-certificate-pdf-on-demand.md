# ADR-0007: Generate Course Certificates as PDF on Demand

## Status
Accepted

## Context
Ao concluir um curso, o aluno deve receber um **certificado**.

O sistema precisa de uma estratégia para:
- disponibilizar o certificado para download
- garantir unicidade (um certificado por aluno/curso)
- permitir verificação futura (via código)
- evitar complexidade operacional no MVP

Alternativas consideradas:

### 1. Gerar e armazenar PDF no banco (BLOB)
- Simples para download direto
- Porém aumenta tamanho do banco rapidamente
- Mais difícil de escalar e fazer backup

### 2. Gerar e armazenar em storage (S3/Blob) após conclusão
- Bom para escala e custos previsíveis
- Requer infraestrutura de storage e políticas de acesso
- Aumenta complexidade do MVP

### 3. Gerar PDF sob demanda (on-demand) a partir dos dados
- Não exige storage adicional no MVP
- Sempre gera a versão “atual” baseada nos dados
- Pode ter custo de CPU em alta demanda, mas aceitável no início

Como o MVP precisa ser simples e o volume inicial é incerto, gerar **PDF sob demanda** é uma solução equilibrada.

## Decision
O certificado será gerado como **PDF on-demand** quando solicitado pelo usuário autenticado.

### Regras
- Certificado é liberado apenas se o curso estiver **100% concluído**.
- Um certificado é **único por (user, course)**.
- Um `certificate_code` único é gerado e armazenado.
- O PDF é gerado usando dados do usuário, curso e data de emissão.

### Endpoints
Conforme `docs/api-spec.md`:

- `GET /courses/{courseId}/certificate`
  - retorna metadados do certificado (quando disponível)

- `GET /courses/{courseId}/certificate/download`
  - retorna o PDF (`application/pdf`)

### Persistência
A tabela `certificates` armazenará:
- `user_id`
- `course_id`
- `issued_at`
- `certificate_code`

Não será armazenado o arquivo PDF.

Schema em:
- `docs/database.md`

## Consequences

### Positive
- Simplicidade operacional no MVP (sem storage dedicado)
- Menos custo de infraestrutura
- Certificado sempre consistente com dados atuais
- Fácil de testar

### Negative
- Geração repetida pode aumentar consumo de CPU
- Pode haver latência no download do PDF em horários de pico

### Mitigations
- Cache futuro (ex.: Redis) para PDF gerado recentemente
- Geração assíncrona e armazenamento em S3 se a demanda crescer
- Rate limit no endpoint de download se necessário

## Notes
Esta decisão impacta:
- Domínio (`docs/domain.md`)
- Banco (`docs/database.md`)
- API (`docs/api-spec.md`)
- Infraestrutura (módulo de geração de PDF)

Evolução futura para storage permanente deve ser registrada em nova ADR.
