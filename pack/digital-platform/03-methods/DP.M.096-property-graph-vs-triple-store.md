---
id: DP.M.096
name: "Выбор Property Graph vs Triple Store для доменной knowledge base с rich metadata"
type: method
status: draft
created: 2026-05-19
valid_from: 2026-05-19
sources:
  - DS-my-strategy commit 166cb17b (feat(WP-338): ArchGate Ф1.1 — выбор Вариант B Property Graph)
related:
  applies_to: [DP.SOTA.004, DP.SOTA.019]
  references: []
  complements: []
---

# DP.M.096 — Выбор Property Graph vs Triple Store для доменной knowledge base

> Когда узлы несут rich metadata и связи строго типизированы — Property Graph; когда приоритет открытый RDF-экспорт и SPARQL-federation — Triple Store.

## Обещание

Дать архитектурный критерий выбора между Property Graph (Neo4j / поверх SQL) и Triple Store (RDF/SPARQL) при построении knowledge base из структурированных доменных Pack'ов.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Нативность rich metadata ↔ открытость RDF-экспорта | Property Graph хранит ~10 frontmatter-полей узла как node properties без reification bloat, но жертвует SPARQL-совместимостью и федерацией с внешними системами — метод заставляет выбрать явно, по критериям 1-3, а не по дефолтной СУБД |
| Скорость traversal ↔ гибкость открытой схемы | Typed labelled edges с индексом дают O(1) hop для запросов «все методы, зависящие от формализации», но закрытая типизация связей не подходит открытому графу, куда пользователи добавляют свои predicates без схемы |

## Вход

- Pack-структуры с frontmatter-метаданными (~10+ полей на узел)
- Typed edges между сущностями (is-a, depends-on, instantiates, extends)
- Требование graph-traversal запросов («найди все методы, зависящие от этой формализации»)

## Выход

- Обоснованный выбор: Property Graph или Triple Store
- Список аргументов для ArchGate (критерии 1-3 ниже)

## Алгоритм выбора

### Выбирай Property Graph если:

1. **Rich metadata на узлах.** Каждый Pack-узел несёт ~10 frontmatter-полей (id, type, status, created, sources, tags…). Property Graph хранит это нативно как node properties. Triple Store требует reification (каждый атрибут = отдельный triple) → многократный рост числа trojek.
2. **Typed relationships.** Связи типа `is-a`, `depends-on`, `instantiates` естественно моделируются как labelled edges. Triple Store представляет их через predicates, но теряет возможность прикрепить свойства к самой связи без ещё одного reification.
3. **Graph traversal как основная операция.** «Все методы, зависящие от этой формализации» — O(1) по hop в Property Graph с индексом. В Triple Store без специального индекса — O(N) full-scan.

### Выбирай Triple Store если:

- Приоритет: RDF-экспорт и SPARQL-совместимость с внешними системами
- Граф открытый (пользователи добавляют свои predicates без схемы)
- Основная операция — pattern-matching по RDF statements, а не hop-traversal

## Применимость

- Domain knowledge bases с Pack-подобной структурой (typed entities + frontmatter)
- Архитектурный выбор при построении любой knowledge graph из структурированных документов
- WP-338 (глобальный граф Pack-репо IWE)

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Epistemic stage в frontmatter не указан._

| Bias | Direction of distortion |
|------|--------------------------|
| Выбор знакомого инструмента раньше применения критериев | Внимание практика уже приковано к знакомой СУБД, и критерии 1-3 используются для пост-фактум обоснования в ArchGate, а не для честного прохода от данных: frontmatter-поля, typed edges, паттерн traversal-запросов |
| Reification-стоимость недооценивается на старте | Внимание съезжает к привлекательным возможностям Triple Store (SPARQL, федерация) и не доходит до подсчёта роста statements (5-10× на ~10 frontmatter-полях узла) — проблема всплывает только как нелинейный latency traversal при росте числа Pack'ов |

## Failure mode без этого выбора

Применение Triple Store для Pack-like структуры: reification bloat → граф в 5-10× больше по числу statements → latency traversal-запросов растёт нелинейно с ростом Pack'ов.

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
