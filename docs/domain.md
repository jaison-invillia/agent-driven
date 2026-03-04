# 📚 Domain Model

Este documento descreve o **modelo de domínio** do Portal Educacional.

Ele define:

-   entidades do sistema
-   relacionamentos
-   regras de negócio
-   estados do domínio

Para detalhes de arquitetura consulte:

➡ docs/architecture.md

Para visão geral do projeto consulte:

➡ README.md

------------------------------------------------------------------------

# 🧩 Entidades do Domínio

As principais entidades do sistema são:

    User
    Course
    Lesson
    CourseProgress
    Certificate

------------------------------------------------------------------------

# 👤 User

Representa um aluno cadastrado na plataforma.

## Responsabilidades

-   autenticar no sistema
-   acessar cursos
-   assistir aulas
-   receber certificados

## Atributos

    id
    name
    email
    passwordHash
    createdAt
    updatedAt

## Regras

-   email deve ser único
-   senha deve ser armazenada criptografada
-   usuário precisa estar autenticado para acessar cursos

------------------------------------------------------------------------

# 📚 Course

Representa um curso disponível na plataforma.

Um curso é composto por várias aulas.

## Atributos

    id
    title
    description
    createdAt
    updatedAt

## Regras

-   um curso deve possuir pelo menos uma aula
-   cursos podem ser acessados apenas por usuários autenticados

------------------------------------------------------------------------

# 🎥 Lesson

Representa uma aula dentro de um curso.

As aulas são hospedadas no YouTube.

## Atributos

    id
    courseId
    title
    description
    youtubeUrl
    order
    createdAt

## Regras

-   cada aula pertence a um único curso
-   aulas devem possuir ordem dentro do curso
-   youtubeUrl deve ser válida

------------------------------------------------------------------------

# 📊 CourseProgress

Representa o progresso de um aluno em um curso.

Cada registro indica se o aluno concluiu uma aula.

## Atributos

    id
    userId
    courseId
    lessonId
    completed
    completedAt

## Regras

-   progresso é registrado por aula
-   um aluno não pode ter progresso duplicado para a mesma aula
-   progresso pertence a um usuário e um curso

------------------------------------------------------------------------

# 🏆 Certificate

Representa um certificado emitido para um aluno.

Certificados são emitidos quando o curso é concluído.

## Atributos

    id
    userId
    courseId
    issuedAt
    certificateCode

## Regras

-   certificado só pode ser emitido se o curso estiver completo
-   certificado deve possuir código único

------------------------------------------------------------------------

# 🔗 Relacionamentos

Relacionamentos principais do domínio:

    User
      │
      │ 1..*
      ▼
    CourseProgress
      ▲
      │
    Lesson ─── belongs to ─── Course
      │
      ▼
    Certificate (after completion)

------------------------------------------------------------------------

# 📊 Regras de Negócio

## Cadastro de usuário

-   usuário cria conta
-   email deve ser único
-   senha deve ser criptografada

------------------------------------------------------------------------

## Acesso aos cursos

-   somente usuários autenticados podem acessar cursos

------------------------------------------------------------------------

## Progresso de aula

Quando um aluno assistir uma aula:

1.  frontend envia evento para API
2.  progresso é registrado
3.  sistema recalcula progresso do curso

------------------------------------------------------------------------

## Conclusão de curso

Um curso é considerado concluído quando:

    todas as aulas do curso estão marcadas como completas

------------------------------------------------------------------------

## Emissão de certificado

Quando o curso estiver completo:

1.  sistema verifica progresso
2.  certificado é gerado
3.  certificado fica disponível para download

------------------------------------------------------------------------

# 🔄 Fluxo do Domínio

Fluxo típico do sistema:

    User registers
            │
            ▼
    User logs in
            │
            ▼
    User selects course
            │
            ▼
    User watches lessons
            │
            ▼
    Progress is recorded
            │
            ▼
    Course completed
            │
            ▼
    Certificate issued

------------------------------------------------------------------------

# 📈 Possíveis Evoluções do Domínio

O modelo pode evoluir para incluir:

-   instrutores
-   avaliações de cursos
-   quizzes
-   trilhas de aprendizado
-   certificados verificáveis
-   gamificação
