---
id: DP.SOTA.025
name: "BORO — 4D Ontology & Naming Pattern"
type: sota
status: active
summary: "SOTA-аннотация вклада BORO в 4D-онтологию (ISO 15926 family) и универсального naming-паттерна как framework-level reusable структуры. trust: hypothesis."
created: 2026-05-20
valid_from: 2026-05-20
trust: hypothesis
revision_criterion: "Проверить: (1) сохраняется ли актуальность 4D-подхода BORO в современных онтологических фреймворках (2026+); (2) применим ли naming-паттерн BORO к новым доменам (≥3) с сохранением компакции. Если оба критерия не выполняются — понизить статус до deprecated."
source: "Partridge C., Business Objects: Re-Engineering for Re-Use, BORO Methodology (1996–2014)"
related:
  informs: [DP.SOTA.023]
  see_also: [DP.SOTA.024, MIM.M.026]
sources:
  - "Partridge C., Business Objects: Re-Engineering for Re-Use (3rd ed. draft, 2014)"
  - "ISO 15926: Industrial automation systems and integration — Integration of life-cycle data"
---

# DP.SOTA.025 — BORO: 4D Ontology & Naming Pattern

> SOTA-аннотация вклада методологии BORO в 4D-онтологию и универсального naming-паттерна. Статус: **hypothesis**.

---

## 1. BORO как первичный источник 4D Ontology

### 1.1 Контекст

Методология **BORO** (Business Objects Re-engineering with Objects, Partridge 1996/2005) — основа 4D-подхода в ISO 15926. BORO показал на конкретных примерах (re-engineering of bank address, bank holiday, weekend, time zone), как entity-формат рефакторится в 4D-объектную модель.

### 1.2 Ключевые примеры

- **Bank holiday** — пересечение объектов `country` и `day` в space-time.
- **Country weekend** — класс классов с temporal-whole-part-of-member tuples.
- **Местное время** — «зигзаг» по space-time, отличный от GMT.

### 1.3 Что берём у BORO дополнительно к ISO 15926

- **Salvage-подход** к смене парадигмы — реинжиниринг существующих entity-моделей без полного переписывания.
- **Aspect vs Whole** как эпистемологический принцип смены парадигмы (Pyramid analogy, Ch.16 §8).

### 1.4 Что НЕ берём

- Полную 4D-реализацию с PossibleIndividual, ребусом whole-part-of-member-tuple-class composite signs и temporal queries — слишком heavy для лёгкого Pack. Достаточно версионирования через `supersedes` + Kinds/Owner Roles.

---

## 2. Universal Naming Pattern

### 2.1 Утверждение

> «It seems likely that all naming models share the same shaped pattern. ... This gives us the general model for the naming pattern shown in Figure 16.18. ... it is independent of both geo-political areas and banks. Furthermore, it has 15 objects of which only 10 are specific to names. This pattern is ubiquitous; its model will be re-used a large number of times in almost every re-engineering. Therefore, it makes sense to put its model into the general lexicon...» (Partridge, Ch.16 §7.1)

### 2.2 Структура

Универсальный naming-паттерн — framework-level reusable структура:
- 15 объектов, из которых 10 специфичны для именования.
- Независим от домена (geo-political areas, banks, и др.).
- Компакция: 200+ attribute types для имён → одна переиспользуемая структура.

### 2.3 Применимость

- При проектировании схем именования в Pack-сущностях.
- Как тест на generalisation: если naming-модель не обобщается за пределы одного домена — она недостаточно abstract.

---

## Связи

- [DP.SOTA.023 Semiotic Engineering](DP.SOTA.023-semiotic-engineering.md) — комплементарно: BORO даёт онтологический фундамент, semiotic engineering — знаковый слой.
- [DP.SOTA.024 BORO Fundamental Particles](DP.SOTA.024-boro-fundamental-particles.md) — родственная SOTA-аннотация по методологии BORO.
- [MIM.M.026](MIM.M.026-re-engineering-method.md) — практический метод re-engineering.
