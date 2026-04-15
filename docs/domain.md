# 📚 Domain Model

<!-- TEMPLATE: Fill this document with your project's domain model. -->
<!-- See docs/engineering-docs-recommendation.md for detailed guidance. -->

This document describes the project's **domain model**.

It defines:

- system entities
- relationships
- business rules
- domain states

For architecture details see: `docs/architecture.md`

For project overview see: `README.md`

---

## 🧩 Domain Entities

<!-- List the system's main entities. Example: -->
<!-- User, Order, Product, Payment, etc. -->

> **[FILL]** List your project's domain entities.

---

## Entity: [Name]

<!-- Repeat this section for each entity. -->

> **[FILL]** Describe each entity following the model below.

### Description

Brief description of the entity and its role in the system.

### Attributes

```
id
[field1]
[field2]
createdAt
updatedAt
```

### Rules

- [Business rule 1]
- [Business rule 2]

---

## 🔗 Relationships

<!-- Describe the relationships between entities. You can use ASCII diagrams or text. -->

> **[FILL]** Map the main relationships (1:N, N:N, etc.)

```
EntityA
  │ 1..*
  ▼
EntityB ─── belongs to ─── EntityC
```

---

## 📊 Business Rules

<!-- List the system's global business rules, grouped by context. -->

> **[FILL]** Describe your domain's business rules.

### [Context 1]

- [Rule]

### [Context 2]

- [Rule]

---

## 🔄 Main Flow

<!-- Describe the system's main flow (happy path). -->

> **[FILL]** Describe the user's main flow in the system.

```
[Step 1] → [Step 2] → [Step 3] → [Result]
```
