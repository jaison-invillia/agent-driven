# ADR-0005: Use YouTube as the Video Hosting Platform

## Status
Accepted

## Context
O Portal Educacional disponibilizará cursos compostos por aulas em vídeo.
Esses vídeos precisam ser armazenados e distribuídos aos alunos com boa performance.

Alternativas consideradas:

### 1. Hospedar vídeos na própria infraestrutura
- Armazenamento em S3 ou similar
- Necessidade de pipeline de upload/transcoding
- Custo de armazenamento e banda
- Necessidade de player customizado

### 2. Plataformas de vídeo dedicadas (Vimeo, Wistia)
- Melhor controle e analytics
- Custos recorrentes
- Overhead operacional maior

### 3. YouTube
- Infraestrutura global de distribuição
- Player robusto e confiável
- Fácil incorporação em aplicações web
- Custo zero para hospedagem básica

Como o objetivo inicial do sistema é **disponibilizar conteúdo educacional com simplicidade e baixo custo**, o YouTube atende bem ao caso de uso.

## Decision
Os vídeos das aulas serão **hospedados no YouTube** e incorporados na aplicação.

A aplicação armazenará apenas a URL do vídeo.

Exemplo:

```
https://www.youtube.com/watch?v=<videoId>
```

Cada aula (`lesson`) terá um campo:

```
youtube_url
```

O frontend exibirá o vídeo utilizando o **player embed do YouTube**.

## Funcionamento

Fluxo típico:

1. Instrutor envia vídeo para o YouTube.
2. O administrador cadastra a aula no sistema com `youtube_url`.
3. O frontend renderiza o player embutido.
4. O backend controla apenas:
   - progresso da aula
   - conclusão
   - certificados.

## Consequences

### Positive
- Zero custo de armazenamento e CDN
- Player robusto e amplamente suportado
- Escala automática para distribuição de vídeo
- Implementação simples no frontend

### Negative
- Controle limitado sobre analytics de vídeo
- Dependência de plataforma externa
- Usuários podem acessar vídeo diretamente se tiverem a URL

### Mitigations
- Utilizar vídeos **não listados** no YouTube.
- Armazenar apenas URLs autorizadas no sistema.
- Futuramente pode-se migrar para solução própria se necessário.

## Notes

Esta decisão impacta:

- Domínio (`docs/domain.md`)
- Modelagem de aulas (`docs/database.md`)
- Implementação do frontend (`Next.js`)

A aplicação **não armazena vídeos**, apenas suas URLs.
