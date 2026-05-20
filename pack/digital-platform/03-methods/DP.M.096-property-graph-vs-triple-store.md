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

## Failure mode без этого выбора

Применение Triple Store для Pack-like структуры: reification bloat → граф в 5-10× больше по числу statements → latency traversal-запросов растёт нелинейно с ростом Pack'ов.
