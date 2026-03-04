# 🗄️ Database

Este documento descreve o **modelo físico de dados** (MySQL 8) do Portal
Educacional.

Para o modelo de domínio e regras de negócio consulte:

-   `docs/domain.md`

Para decisões de arquitetura consulte:

-   `docs/architecture.md`

------------------------------------------------------------------------

# ✅ Princípios

-   **MySQL 8** como banco principal.
-   **FKs e constraints** habilitadas (InnoDB).
-   **Chaves artificiais** (`BIGINT UNSIGNED AUTO_INCREMENT`) para
    entidades principais.
-   **Auditoria mínima** com `created_at` / `updated_at`.
-   **Unicidade** garantida por índices únicos (ex.: `users.email`,
    progresso por aula, certificado por curso).
-   **Integridade referencial** com FKs e regras de
    `ON DELETE`/`ON UPDATE`.

------------------------------------------------------------------------

# 🧱 Tabelas

## 1) `users`

Armazena alunos cadastrados.

### Campos

-   `id`: identificador interno
-   `name`: nome do aluno
-   `email`: email único
-   `password_hash`: hash da senha
-   `created_at`, `updated_at`

### Regras/Constraints

-   `email` **único**
-   `password_hash` obrigatório

------------------------------------------------------------------------

## 2) `courses`

Armazena cursos disponíveis.

### Campos

-   `id`
-   `title`
-   `description`
-   `created_at`, `updated_at`

### Regras/Constraints

-   `title` obrigatório

------------------------------------------------------------------------

## 3) `lessons`

Armazena aulas que pertencem a um curso.

### Campos

-   `id`
-   `course_id` (FK → `courses.id`)
-   `title`
-   `description`
-   `youtube_url`
-   `display_order` (ordem da aula no curso)
-   `created_at`

### Regras/Constraints

-   `course_id` obrigatório
-   `youtube_url` obrigatório
-   `display_order` obrigatório
-   Unicidade por curso: `(course_id, display_order)`

------------------------------------------------------------------------

## 4) `course_progress`

Armazena o progresso do aluno por aula (nível de granularidade:
**lesson**).

### Campos

-   `id`
-   `user_id` (FK → `users.id`)
-   `course_id` (FK → `courses.id`)
-   `lesson_id` (FK → `lessons.id`)
-   `completed` (0/1)
-   `completed_at` (timestamp de conclusão)
-   `created_at`, `updated_at`

### Regras/Constraints

-   Progresso **não pode duplicar** para o mesmo aluno/aula:
    `(user_id, lesson_id)` **único**
-   `course_id` deve ser consistente com o `lesson_id` (regra de
    domínio).
    -   **Nota**: a consistência
        `lesson.course_id == course_progress.course_id` pode ser
        validada no Service Layer (ou via trigger, se desejado).
-   Se `completed = 1` então `completed_at` deve estar preenchido
    (validação no Service Layer).

------------------------------------------------------------------------

## 5) `certificates`

Armazena certificados emitidos após conclusão do curso.

### Campos

-   `id`
-   `user_id` (FK → `users.id`)
-   `course_id` (FK → `courses.id`)
-   `issued_at`
-   `certificate_code` (código público verificável)
-   `created_at`

### Regras/Constraints

-   Certificado é **único por aluno e curso**: `(user_id, course_id)`
    **único**
-   `certificate_code` **único**
-   Emissão só pode ocorrer se o curso estiver completo (regra de
    domínio no Service Layer)

------------------------------------------------------------------------

# 🧠 Observações importantes (Domínio ↔ Banco)

## Cálculo de conclusão de curso

O curso é considerado concluído quando:

-   total de aulas do curso = total de aulas marcadas como concluídas
    pelo aluno

Estratégia recomendada: - Backend consulta: `COUNT(lessons)` vs
`COUNT(course_progress WHERE completed=1)` - Ao atingir 100%, emitir
registro em `certificates`

------------------------------------------------------------------------

# 🧾 Schema SQL (MySQL 8)

> Este bloco é a **referência base** para migrations (DDL).\
> Ajustes finos podem ser necessários conforme framework/ORM escolhido.

``` sql
-- MySQL 8.x
-- Engine padrão recomendada: InnoDB
-- Charset recomendado: utf8mb4

CREATE TABLE users (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  name VARCHAR(120) NOT NULL,
  email VARCHAR(190) NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  UNIQUE KEY uq_users_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE courses (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  title VARCHAR(180) NOT NULL,
  description TEXT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  KEY idx_courses_title (title)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE lessons (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  course_id BIGINT UNSIGNED NOT NULL,
  title VARCHAR(180) NOT NULL,
  description TEXT NULL,
  youtube_url VARCHAR(500) NOT NULL,
  display_order INT UNSIGNED NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  CONSTRAINT fk_lessons_course
    FOREIGN KEY (course_id) REFERENCES courses(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  UNIQUE KEY uq_lessons_course_order (course_id, display_order),
  KEY idx_lessons_course (course_id),
  KEY idx_lessons_course_order (course_id, display_order)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE course_progress (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  user_id BIGINT UNSIGNED NOT NULL,
  course_id BIGINT UNSIGNED NOT NULL,
  lesson_id BIGINT UNSIGNED NOT NULL,
  completed TINYINT(1) NOT NULL DEFAULT 0,
  completed_at TIMESTAMP NULL DEFAULT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  CONSTRAINT fk_progress_user
    FOREIGN KEY (user_id) REFERENCES users(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CONSTRAINT fk_progress_course
    FOREIGN KEY (course_id) REFERENCES courses(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CONSTRAINT fk_progress_lesson
    FOREIGN KEY (lesson_id) REFERENCES lessons(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  UNIQUE KEY uq_progress_user_lesson (user_id, lesson_id),
  KEY idx_progress_user_course (user_id, course_id),
  KEY idx_progress_course_lesson (course_id, lesson_id),
  KEY idx_progress_completed (completed)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE certificates (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  user_id BIGINT UNSIGNED NOT NULL,
  course_id BIGINT UNSIGNED NOT NULL,
  issued_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  certificate_code VARCHAR(64) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  CONSTRAINT fk_cert_user
    FOREIGN KEY (user_id) REFERENCES users(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CONSTRAINT fk_cert_course
    FOREIGN KEY (course_id) REFERENCES courses(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  UNIQUE KEY uq_cert_user_course (user_id, course_id),
  UNIQUE KEY uq_cert_code (certificate_code),
  KEY idx_cert_user (user_id),
  KEY idx_cert_course (course_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

------------------------------------------------------------------------

# 🧪 Dados mínimos de teste (opcional)

-   Criar 1 curso com 3+ aulas
-   Criar 1 usuário
-   Marcar progresso aula a aula
-   Validar emissão de certificado quando completar todas as aulas

------------------------------------------------------------------------

# 🔄 Migrations

Recomendação: - manter migrations versionadas em
`backend/src/database/migrations` - migrations **idempotentes** (ou com
controle de versionamento pelo migrator) - aplicar migrations
automaticamente em ambientes de dev/staging via CI quando possível

------------------------------------------------------------------------

# 🔍 Índices e Performance

Índices críticos já previstos: - `users.email` (login) -
`lessons (course_id, display_order)` (lista ordenada de aulas) -
`course_progress (user_id, course_id)` (dashboard / progresso do
curso) - `certificates (user_id, course_id)` (verificação de
certificado)

Se houver necessidade de relatórios mais pesados, considerar: - índices
adicionais conforme queries reais - caching (ex.: Redis) para endpoints
de leitura intensiva
