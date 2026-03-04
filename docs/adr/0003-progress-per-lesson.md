# ADR-0003: Track Course Progress Per Lesson Completion

## Status
Accepted

## Context
O Portal Educacional precisa registrar o **progresso do aluno em um curso** para:

- saber quais aulas já foram assistidas
- calcular porcentagem de conclusão do curso
- liberar emissão de certificado quando o curso estiver completo

Algumas alternativas consideradas:

### 1. Progresso por tempo assistido (video tracking)
- medir quanto do vídeo foi assistido
- depende de integração profunda com player de vídeo
- difícil garantir confiabilidade
- aumenta complexidade do frontend

### 2. Progresso por percentual de vídeo
- registrar percentual assistido
- ainda depende de player e eventos de vídeo
- pode gerar inconsistências

### 3. Progresso por **aula concluída**
- o aluno marca a aula como concluída
- backend registra conclusão
- progresso do curso é calculado baseado no número de aulas concluídas

Como os vídeos estão hospedados no **YouTube** e o sistema não controla diretamente o player, uma abordagem baseada em **aula concluída** é mais simples, confiável e fácil de manter.

## Decision

O progresso do curso será controlado por **conclusão de aula (lesson completion)**.

### Funcionamento

1. O aluno acessa uma aula.
2. Após assistir, o frontend chama o endpoint:

```
POST /courses/{courseId}/lessons/{lessonId}/complete
```

3. O backend registra a conclusão na tabela `course_progress`.
4. O progresso do curso é calculado com base em:

```
completed_lessons / total_lessons
```

5. Quando `completed_lessons == total_lessons`, o curso é considerado **concluído**.

6. Neste momento, o sistema pode emitir um certificado.

### Idempotência

A operação de concluir aula deve ser **idempotente**.

Se o aluno tentar concluir a mesma aula novamente:

- o sistema não deve duplicar registros
- o endpoint deve retornar sucesso mantendo o estado atual

### Modelagem de dados

Tabela principal:

```
course_progress
```

Campos relevantes:

- `user_id`
- `course_id`
- `lesson_id`
- `completed_at`

Constraint importante:

```
UNIQUE(user_id, lesson_id)
```

Isso garante que a mesma aula não seja registrada duas vezes para o mesmo usuário.

Definição completa do schema em:

- `docs/database.md`

### Cálculo de progresso

Endpoint:

```
GET /courses/{courseId}/progress
```

Retorna:

- total de aulas
- aulas concluídas
- porcentagem de conclusão
- se certificado está disponível

Contrato completo:

- `docs/api-spec.md`

## Consequences

### Positive

- Implementação simples e confiável
- Não depende do player de vídeo
- Fácil de testar
- Fácil de manter
- Reduz complexidade de frontend

### Negative

- Não garante que o aluno realmente assistiu todo o vídeo
- Usuário pode marcar aula como concluída sem assistir

### Mitigations

- UX pode incentivar assistir antes de liberar botão "concluir"
- Futuramente pode-se adicionar eventos do player para enriquecer tracking

## Notes

Esta decisão impacta:

- **Domínio** (`docs/domain.md`)
- **Banco de dados** (`docs/database.md`)
- **API** (`docs/api-spec.md`)
