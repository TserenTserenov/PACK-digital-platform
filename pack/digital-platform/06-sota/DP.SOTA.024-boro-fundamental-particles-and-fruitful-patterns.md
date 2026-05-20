---
id: DP.SOTA.024
name: "BORO Methodology — Fundamental Particles & Fruitful Patterns"
type: sota
status: active
summary: "SOTA-аннотация методологии BORO (Business Objects Re-Engineering for Re-Use, Partridge): фундаментальные онтологические частицы и гипотеза о межпроектной fruitfulness паттернов. trust: hypothesis."
created: 2026-05-20
valid_from: 2026-05-20
trust: hypothesis
revision_criterion: "Появление эмпирического сравнения BORO с альтернативными онтологическими подходами (DOLCE, BFO, UFO) на конкретных бизнес-доменах с метриками re-use/accuracy/compactness — пересмотреть статус. Для fruitfulness: сформировать список patterns, признанных fruitful в одном проекте; через 6-12 мес. проверить переиспользуемость в новых проектах. <20% → опровергнуть."
source: "Partridge C., Business Objects: Re-Engineering for Re-Use, BORO Methodology (1996–2014)"
related:
  informs: [DP.SOTA.007, DP.SOTA.017, DP.SOTA.023]
  see_also: [MIM.SOTA.001, MIM.M.026]
sources:
  - "Partridge C., Business Objects: Re-Engineering for Re-Use (3rd ed. draft, 2014)"
  - "Partridge C., BORO Ch 18 §3.2.1 (1996)"
---

# DP.SOTA.024 — BORO Methodology: Fundamental Particles & Fruitful Patterns

> SOTA-аннотация ключевых инсайтов методологии BORO (Business Objects Reference Ontology, Chris Partridge, 1996+). Статус: **hypothesis** — авторская методология одного исследователя, требующая независимой валидации.

---

## 1. Фундаментальные частицы информационной парадигмы

### 1.1 Контекст

BORO — методология реинжиниринга бизнес-моделей с переходом от entity-парадигмы к object-парадигме через смену фундаментальных онтологических частиц.

### 1.2 Ключевой инсайт

Информационная парадигма построена вокруг **фундаментальных частиц** — базовых типов сущностей, из которых конструируются модели. Реинжиниринг парадигмы = смена набора частиц.

| Парадигма | Фундаментальные частицы |
|-----------|------------------------|
| Entity-парадигма | 4 частицы: entity types, entities, attribute types, attributes |
| Object-парадигма (BORO) | 1 частица: objects |

Сокращение онтологического зоопарка с 4 до 1 даёт ту же выгоду, что и переход от эпициклов к эллипсам Кеплера: более компактные, точные, обобщаемые модели.

### 1.3 Ontic commitment

Любая бизнес-модель **неизбежно** делает онтологическое обязательство: вводя термины («car», «date», «amount»), модельер автоматически коммитится к их типу (entity, object, иное). Игнорирование стадии моделирования не отменяет коммитмент — переносит его в подсознание разработчика.

### 1.4 Применимость для цифровой платформы

- При проектировании Pack-сущностей и индикаторов цифрового двойника — явный выбор частиц важнее интуитивного.
- Для обсуждения архитектуры: «какие у нас фундаментальные частицы для домена X?» — конструктивный вопрос.
- BORO как ontology — потенциальный кандидат для семантического слоя knowledge graph.

### 1.5 Граница

- BORO разработан для бизнес-моделирования в финансовом секторе (1990–2000). Применимость к ИИ-системам/агентам — требует адаптации.
- Утверждение «1 частица лучше 4» — авторская позиция Partridge, не consensus в DDD/ontology engineering community.

---

## 2. Fruitful Patterns Hypothesis

### 2.1 Утверждение

> Если паттерн признан «fruitful» (обнаружил высокий уровень generalisation внутри scope), его fruitfulness, как правило, **расширяется за пределы declared scope** — паттерн оказывается применим к ситуациям, которые не рассматривались при моделировании.

(Partridge, BORO Ch 18 §3.2.1, 1996)

Примеры из BORO: naming pattern, spatio-temporal patterns (geographic regions, time zones) — обнаружены в спот-моделировании, реиспользованы для banking address, holiday processing, transaction modelling.

### 2.2 Следствия для практики

1. **Inversion of estimation:** Inside-scope generalisation — leading indicator of cross-project reusability.
2. **Investment justification:** Тратить дополнительное время на доведение паттерна до общей формы внутри проекта — оправдано.
3. **Pattern catalog:** Имеет смысл выделять «fruitful» паттерны в каталог для проверки при старте нового проекта.

### 2.3 Альтернативные позиции

- **Traditional view:** Pattern is fruitful within its scope only; cross-project reuse требует отдельной работы.
- **DDD-view:** Bounded context естественно ограничивает применимость pattern; reuse за пределами BC — анти-паттерн.

### 2.4 Эмпирические свидетельства

- BORO worked examples (Partridge, 1996) — naming, country, address, holiday.
- Gang-of-Four patterns (Gamma et al., 1994) — паттерны, разработанные в одном контексте (GUI/desktop), оказались fruitful в server, distributed, ML-системах.

---

## Связи

- [DP.SOTA.007 AI Ontology Engineering](DP.SOTA.007-ai-ontology-engineering.md) — пересечение по теме «явная онтология».
- [DP.SOTA.017 Concept Graphs](DP.SOTA.017-concept-graphs.md) — BORO objects могут служить узлами концепт-графа.
- [DP.SOTA.023 Semiotic Engineering](DP.SOTA.023-semiotic-engineering.md) — комплементарно: BORO даёт онтологический фундамент.
- [MIM.SOTA.001](MIM.SOTA.001-modelling-paradigm-history.md) — историческая эволюция парадигм.
- [MIM.M.026](MIM.M.026-re-engineering-method.md) — метод re-engineering через reverse + forward engineering.
