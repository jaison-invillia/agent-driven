# 🗄️ Database

<!-- TEMPLATE: Fill this document with your project's database schema. -->
<!-- See docs/engineering-docs-recommendation.md for detailed guidance. -->

This document describes the project's **physical data model**.

For the domain model and business rules see: `docs/domain.md`

For architecture decisions see: `docs/architecture.md`

---

## ✅ Principles

- **[FILL]** Chosen database (e.g.: PostgreSQL 16, MySQL 8, MongoDB 7, SQLite, etc.)
- **FKs and constraints** enabled (for relational databases).
- **Artificial keys** for main entities (e.g.: `BIGINT`, `UUID`, `SERIAL` — per database convention).
- **Minimum auditing** with `created_at` / `updated_at`.
- **Uniqueness** enforced by unique indexes per domain rules.
- **Referential integrity** with FKs and `ON DELETE`/`ON UPDATE` rules (when applicable).

---

## 🧱 Tables

<!-- Repeat this section for each system table. -->

### [table_name]

> **[FILL]** Describe each table following the model below.

**Description:** Brief description of the table's purpose.

#### Fields

- `id`: internal identifier
- `[field]`: description
- `created_at`, `updated_at`

#### Rules/Constraints

- [Constraint 1, e.g.: field X **unique**]
- [Constraint 2, e.g.: FK to table Y]

---

## 🧠 Important observations (Domain ↔ Database)

<!-- Describe calculations, derivations, or rules that connect domain and database. -->

> **[FILL]** Document business rules that impact the schema (e.g.: completeness calculations, validations in the Service Layer).

---

## 🧾 SQL Schema

<!-- Paste the reference DDL for migrations. -->

> **[FILL]** Add the project's base SQL schema.

```sql
-- [FILL] Chosen database and recommended configurations

-- Example:
-- CREATE TABLE [name] (
--   id [PRIMARY_KEY_TYPE],
--   ...
--   PRIMARY KEY (id)
-- );
```
