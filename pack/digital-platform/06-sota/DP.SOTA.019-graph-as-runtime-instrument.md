---
id: DP.SOTA.019
name: Граф как runtime-инструмент агента + наблюдаемость
type: sota
status: active
valid_from: 2026-04-24
summary: "Паттерны использования concept-графа агентом в runtime (Graph-RAG 2024-2026) + observability KG в продакшене + feedback loop от usage к эволюции графа. Дополняет DP.SOTA.004 (общая технология) и DP.SOTA.017 (структурная гигиена)."
related:
  complements: [DP.SOTA.004, DP.SOTA.017, DP.SOTA.015]
  informs: [WP-242]
  uses: [DP.SOTA.018]
tags: [graph-rag, observability, mcp-tool-design, feedback-loop, retrieval, hipporag, lightrag, ragas, langfuse]
sources:
  - https://arxiv.org/abs/2405.14831
  - https://arxiv.org/abs/2502.14802
  - https://arxiv.org/abs/2410.05779
  - https://arxiv.org/html/2404.16130v2
  - https://arxiv.org/abs/2501.13956
  - https://arxiv.org/abs/2412.15235
  - https://arxiv.org/abs/2506.02404
  - https://arxiv.org/abs/2506.05690
  - https://arxiv.org/html/2510.13193v1
  - https://arxiv.org/abs/2503.14802
  - https://arxiv.org/abs/2509.15464
  - https://microsoft.github.io/graphrag/
  - https://neo4j.com/nodes-2025/agenda/enhancing-retrieval-augmented-generation-with-graphrag-patterns-in-neo4j/
  - https://github.com/getzep/graphiti
  - https://github.com/OSU-NLP-Group/HippoRAG
  - https://github.com/hkuds/lightrag
  - https://github.com/modelcontextprotocol/servers/tree/main/src/memory
  - https://docs.ragas.io/en/stable/
  - https://deepeval.com/docs/metrics-ragas
  - https://langfuse.com/blog/2025-10-28-rag-observability-and-evals
  - https://opensearch.org/blog/introducing-reciprocal-rank-fusion-hybrid-search/
  - https://medium.com/@claudiubranzan/from-llms-to-knowledge-graphs-building-production-ready-graph-systems-in-2025-2b4aff1ec99a
  - https://ojs.aaai.org/index.php/AAAI-SS/article/view/36888
  - https://modelcontextprotocol.io/specification/2025-06-18/server/tools
  - https://blog.arcade.dev/mcp-tool-patterns
---

# DP.SOTA.019 — Граф как runtime-инструмент агента + наблюдаемость

> **Контекст WP-242 Ф8.0.** Исследование (24 апр 2026) для системного плана «граф как инструмент + observability + feedback loop».
>
> **Отличие от соседних SOTA:**
> - **DP.SOTA.004** — общий GraphRAG как технология retrieval
> - **DP.SOTA.017** — гигиена графа (orphan-prevention, editorial pipeline, bilingual patterns)
> - **DP.SOTA.019 (этот)** — **runtime-использование готового графа агентом** + инструментовка его работы + обратная связь от usage к эволюции

## 1. Контекст применения

IWE имеет ручной concept graph (1185 понятий / 3523 рёбра / типы `related/specializes/part_of/prerequisite` / уровни `pack/guide/fpf/course/zp` / bilingual RU+EN / Neon Postgres). Граф построен, но runtime-агенты (Claude Code, бот, ролевые агенты Портной/Оценщик/Навигатор) его не используют — `knowledge_search` делает только embedding-поиск по чанкам документов.

**Задачи:** (a) runtime-API обхода графа, (b) observability за использованием, (c) feedback loop от usage к эволюции.

## 2. Таблица паттернов применимости

| Паттерн | Источник | IWE | Почему |
|---------|----------|-----|--------|
| **Personalized PageRank от query-seeds** | HippoRAG 1/2 ([arxiv 2405.14831](https://arxiv.org/abs/2405.14831), [2502.14802](https://arxiv.org/abs/2502.14802)) | ✅ **да** | Естественный primitive для ручного графа; реализуется в Postgres или TS без ML-обучения |
| **Dual-level retrieval (entity + concept)** | LightRAG ([arxiv 2410.05779](https://arxiv.org/abs/2410.05779)) | ✅ **да** | Ложится на Pack (low) + FPF (high); две параллельные ветки + RRF-fusion |
| **Bi-temporal рёбра (t_valid / t_invalid)** | Zep/Graphiti ([arxiv 2501.13956](https://arxiv.org/abs/2501.13956)) | ✅ **да** | `valid_from` / `superseded_by` уже заявлены в CLAUDE.md §4 |
| **Retrieval без LLM в hot path** | Graphiti (P95 300ms) | ✅ **да** | Критично для bot/VS Code latency — держать LLM вне retrieval |
| **Ontology-constrained subgraph** | OG-RAG ([arxiv 2412.15235](https://arxiv.org/abs/2412.15235)) | ✅ **да** | Прямо использует FPF→Pack иерархию IWE |
| **3-4 composite MCP tools (не 8+ примитивов)** | MemPalace, Arcade 54 patterns | ✅ **да** | Экономит tokens, понятнее LLM |
| **RRF fusion (dense + sparse + graph)** | Zep, GAHR-MSR, [OpenSearch](https://opensearch.org/blog/introducing-reciprocal-rank-fusion-hybrid-search/) | ✅ **да** | Дефолт индустрии, `k=60` константа, не требует tuning |
| **Query expansion через граф** | Neo4j Advanced RAG | ✅ **да** | Fan-out 1-hop от linked-концептов перед embedding-поиском |
| **Graceful degradation (граф недоступен → embedding-only)** | Memento MCP | ✅ **да** | Response envelope flag `graph_enabled: false` |
| **Query router (простые → vector, multi-hop → graph)** | «When to use Graphs» ([arxiv 2506.05690](https://arxiv.org/abs/2506.05690)) | ✅ **да** | GraphRAG хуже naive RAG на fill-in-blank; нужен классификатор |
| **OTel + Langfuse с graph attrs** | Langfuse 2025 | ✅ **да** | Уже в плане (DB #8 health); OTel-native, self-hostable |
| **Ragas context-metrics + groundedness** | [Ragas](https://docs.ragas.io/en/stable/), DeepEval | ✅ **да** | Стандарт индустрии 2025-2026 |
| **Community summaries (lazy, on-demand)** | LazyGraphRAG, Microsoft 2025 | 🟡 частично | Для FPF-веток как 2-й tier; не нужно на MVP |
| **Cypher + vector hybrid** | Neo4j GraphRAG 2025 | 🟡 идея | IWE не на Neo4j; портируется как SQL+pgvector+JOIN |
| **Entity linking через GLiNER** | SemTab 2025 | 🟡 частично | Bilingual bonus; простой `name_ru`/`name_en` match может хватить |
| **LLM-guided traversal** | ReMindRAG NeurIPS'25 ([arxiv 2510.13193](https://arxiv.org/html/2510.13193v1)) | ❌ на MVP | Дорого (N LLM calls на обход); tier-3 на горизонт Q3+ |
| **Anthropic KG Memory write-stack** | [MCP servers/memory](https://github.com/modelcontextprotocol/servers/tree/main/src/memory) | ❌ | IWE — read-mostly; write-tools не нужны (радикально упрощает API) |
| **Letta/MemGPT blocks** | Letta | ❌ | Agent self-memory, не shared KG — другая модель |
| **Confidence-based KGC (TransE, RotatE)** | Классика | ❌ на MVP | Требует training; co-occurrence сигнал дешевле и даёт первую win |

## 3. Наблюдаемость: метрики

### 3.1 Стандарт индустрии 2025-2026 (Ragas / DeepEval / TruLens)

| Метрика | Что измеряет | Применимо к IWE |
|---------|--------------|------------------|
| Context precision | Доля релевантных документов в retrieved | ✅ offline eval |
| Context recall | Полнота retrieved | ✅ offline eval |
| Context relevancy | Семантическое соответствие | ✅ |
| Faithfulness / Groundedness | Ответ выводится из retrieved | ✅ |
| Answer relevancy | Адекватность ответа вопросу | ✅ |
| Citation accuracy | Корректность цитирования | ✅ — прямой сигнал для SC «Ontology-Grounded Answers» |

### 3.2 KG-specific метрики (production-практика 2025)

Из [Claudiu Branzan 2025](https://medium.com/@claudiubranzan/from-llms-to-knowledge-graphs-building-production-ready-graph-systems-in-2025-2b4aff1ec99a) и [AAAI 2025 Key KG Quality](https://ojs.aaai.org/index.php/AAAI-SS/article/view/36888):

| Метрика | Детектор | Действие при сигнале |
|---------|----------|----------------------|
| `orphan_node_rate` | `nodes WHERE id NOT IN (edges.src ∪ edges.dst)` | Автор Pack → привязать или удалить |
| `relationship_density` | `edges / nodes` | Тренд вниз = граф стагнирует |
| `confidence_distribution` | Гистограмма `edge.weight` | P10 низкий → пересмотр embedding-based рёбер |
| `completeness` (missing expected edges) | LLM/rule-based диагностика | Pack-автор → дополнить |
| `consistency` (противоречия) | Грубое: circular specializes / misconception как authoritative | Ревю |
| `freshness` | `now - updated_at` per концепт | Stale концепты → ревю актуальности |

### 3.3 Runtime-usage метрики (пространство для IWE)

⚠️ **Важная находка:** «concept citation rate», «graph coverage», «traversal utility» — **не стандартизованы** в публичной литературе 2025. GraphRAG-Bench ([arxiv 2506.02404](https://arxiv.org/abs/2506.02404), ICLR'26) даёт end-to-end eval пайплайна, но не per-edge / per-node usage метрики. Это ниша для IWE — собственные формулировки не будут изобретением велосипеда.

Кандидаты определений для IWE:

| Метрика | Формула | Смысл |
|---------|---------|-------|
| `concept_citation_rate` | ответы с ≥1 ссылкой на код концепта / все ответы | Доля ответов, явно грунтованных в онтологии |
| `graph_coverage` | узлы, затронутые retrieval за период / всего узлов | Какая часть графа живёт |
| `traversal_utility` | cited_concepts_via_traversal / total_traversed | Платят ли рёбра за свой обход |
| `stale_citation_count` | ответы со ссылкой на удалённый/переименованный концепт | Прямой сигнал инварианта SC |
| `misconception_as_auth_count` | ответы, использовавшие `misconception` как истину | Критический сигнал |
| `edge_traversal_frequency` | per-edge counter за N дней | Кандидаты мёртвых рёбер |

### 3.4 Инструментация

**Рекомендация:** Langfuse (уже упоминается в `distinctions.md` Observability ≠ Health + WP-192 контекст). OTel-native, Apache 2.0, self-hostable.

Custom attributes на span'ах:
- `graph.query.seed_concepts` — какие концепты взяты как seeds
- `graph.traversal.edge_types` — типы обхоженных рёбер
- `graph.traversal.depth` — глубина обхода
- `graph.result.cited_concepts` — какие концепты вошли в контекст LLM
- `graph.result.graph_enabled` — флаг degrade-режима

Ragas как offline eval-framework — batch-прогон golden-set вопросов через knowledge-mcp, метрики попадают в Langfuse или отдельную таблицу.

## 4. MCP-tool API: каноничный набор

Консенсус 2025 (из Anthropic reference, Memento, Graphiti, knowledgegraph-mcp, Zep):

| Tool | Сигнатура | Роль |
|------|-----------|------|
| `search_nodes` | `(query, limit) → [{id, name, type}]` | Точечный поиск по имени/embedding |
| `open_nodes` / `get_nodes` | `(ids[]) → [{node, edges}]` | Получить узлы + их рёбра |
| `neighbors` | `(id, edge_types, limit) → [...]` | Соседи с фильтром |
| `traverse` / `path` | `(from, to, max_hops) → [path]` | Путь между узлами |
| `subgraph` / `expand_context` | `(seeds[], hops, edge_types) → {nodes, edges}` | Induced subgraph — **композит, экономит round-trips** |

**Паттерн:** стартовать с **3-4 composite tools** (не 8+ примитивов). Добавлять примитивы по факту нужды.

## 5. Feedback loop: observability → эволюция графа

| Сигнал | Детектор | Действие |
|--------|----------|----------|
| Dead edge (ребро не обходится N дней) | `edge_traversal_frequency < threshold` | Автор → ревю: устарело / not-discoverable |
| Missing edge (частый co-retrieval без ребра) | Co-occurrence анализ трейсов + path returning empty | LLM/автор → предложить `related(X,Y)` |
| Stale concept | `max(mentioned_at) + low traversal` | Автор → `superseded_by` или рефреш |
| High-confidence but unused | Confidence ≥ P90 + traversal ≈ 0 | Проблема discoverability, не качества |

**Практика:** weekly drift-report в `DS-my-strategy/inbox/` как WP-кандидат. Соответствует паттерну «ТО ≠ Sync ≠ Review» (distinctions.md авторские, WP-217).

**Важно:** baseline usage собирается минимум 30 дней до первого drift-report, иначе false positives забьют автора Pack.

## 6. Не-очевидные находки (меняют пространство решений)

### 6.1 KG-specific runtime метрики — не стандарт
`concept_citation_rate` / `graph_coverage` / `traversal_utility` не имеют канонических определений в SOTA 2025. Это ниша для IWE — определяем сами, не нарушая индустриальные нормы.

### 6.2 LazyGraphRAG: 99.9% стоимости — в query-time
Не нужно batch-пересобирать community summaries для FPF-веток. On-demand + кэш. Меняет ROI 2-го tier (сначала думалось дорого — оказалось дешёво).

### 6.3 Retrieval БЕЗ LLM — индустриальный стандарт hot-path
Zep/Graphiti/HippoRAG 2 держат LLM ВНЕ retrieval (embedding + PPR + BM25 + RRF = чистая алгебра). P95 300ms. LLM-guided traversal (ReMindRAG) — только для сложных tier-2 запросов.

### 6.4 Ручной граф IWE — редкий актив
Большинство 2024-2025 Graph-RAG пейперов фокусируется на LLM-extraction. Noise amplification — известная проблема автографов. У IWE граф вычитанный, значит применимы алгоритмы, которые **не переобучаются** на auto-graph noise → упрощает implementation.

### 6.5 RRF перестал быть research-темой
OpenSearch/Elasticsearch/Azure Cognitive Search имеют встроенный RRF. `k=60` — стандартная константа. Не исследовать «какой fusion», просто использовать.

### 6.6 IWE граф — read-mostly
В отличие от Memento/Graphiti (agent пишет воспоминания), IWE граф редактируется редко и вручную. **Радикально упрощает MCP-API:** 3-4 read-only composite tool вместо 15+ write+read. Не воспроизводить write-stack.

### 6.7 Двуязычность через EN-pivot — подтверждённый стандарт
BabelNet, Concept-pedia, NounAtlas — все используют pivot-language. Текущая `name_ru` / `name_en` fallback в IWE — канонична, не нужно пересматривать.

### 6.8 «Concept citation» — промпт-инжиниринг, не retrieval
OG-RAG достигает +30% attribution не алгоритмом retrieval, а явным output-format в промпте. Для IWE: `knowledge_search` может возвращать `concept_ids`, но LLM не будет их цитировать без инструкции в tool description / system prompt.

### 6.9 GraphRAG хуже naive RAG на простых вопросах
«When to use Graphs in RAG» ([arxiv 2506.05690](https://arxiv.org/abs/2506.05690)): fill-in-blank / point-fact выигрывают от precision и страдают от graph-noise. **Нужен query-router** — простые → vector-only, multi-hop → graph-augmented. Это явный архитектурный компонент, не прямой «включили граф везде».

### 6.10 «Concept-KG ≠ Memory, но связаны»
Mem0, Letta, Zep разделяют shared concept-KG и user-memory. Для IWE это прямо отражает разделение Persona/Память/Контекст (DP.D.050) — в SOTA это **разные backend-ы**, не один слой.

### 6.11 «Какой граф-БД» — меньше важно, чем паттерн retrieval
Kuzu / Neo4j / Apache AGE / pgvector+JOIN — все конкурируют на одной производительности для графов &lt;10M рёбер. IWE (3523 ребра) заведомо ниже любого порога; технический выбор БД не блокирует дизайн.

### 6.12 Мёртвые рёбра — тонкий сигнал
Отсутствие traversal может значить: (а) ребро неправильное, (б) нет запросов этой темы, (в) ребро важное, но indirect. Нужен минимум 30 дней usage-baseline до первого drift-report, иначе false positives забьют автора Pack.

## 7. Рекомендации для IWE

### 7.1 Архитектурная ось (три опоры retrieval)

1. **HippoRAG 2 PPR** — основной primitive обхода ручного графа
2. **LightRAG dual-level** — разделение запросов на entity-level (Pack) и concept-level (FPF)
3. **Graphiti паттерн** — retrieval без LLM в hot path + hybrid с RRF

### 7.2 MCP API (минимум)

3-4 composite read-only tools:
- `knowledge_concept_search(query, levels, limit)` — точечный поиск
- `knowledge_concept_expand(concept_id, hops, edge_types)` — subgraph вокруг
- `knowledge_concept_rank(seeds[], edge_weights)` — PPR от seeds
- `knowledge_search` (существующий) — enrichment `concept_anchors` + 1-hop соседей

### 7.3 Observability слой

- **Транспорт:** OpenTelemetry spans
- **Backend:** Langfuse (self-hosted, OTel-native)
- **Custom attrs:** `graph.query.*`, `graph.traversal.*`, `graph.result.*`
- **Хранение метрик:** `platform.health.*` (по HD «Системная БД ≠ Health-хранилище»)
- **Eval-harness:** Ragas golden-set, weekly batch

### 7.4 Метрики для MVP

Базовые (первые 30 дней):
- `orphan_node_rate` (существующий сигнал)
- `edge_traversal_frequency` (per-edge counter)
- `concept_citation_rate` (per-response)
- `stale_citation_count` (критический)

Продвинутые (после baseline):
- `graph_coverage`
- `traversal_utility`
- Ragas context-precision/recall/groundedness

### 7.5 Что НЕ брать на MVP

- LLM-guided traversal (ReMindRAG)
- Community summaries (LazyGraphRAG) — только после MVP сигнала «нужно»
- KG Completion через embedding-training (TransE и пр.)
- Neo4j backend (pgvector+JOIN достаточно)

### 7.6 Последовательность

1. **Ф8.4** — `concept_expand` + per-edge traversal counter (минимальный runtime-use + observability)
2. **Ф8.5** — enrichment `knowledge_search` + `concept_anchors` в response
3. **Ф8.6** — PPR (HippoRAG-style) + dual-level router
4. **Ф8.7** — feedback loop: weekly drift-report (orphans / dead-edges / co-retrieval suggested edges)
5. **Ф8.8** — onboarding ролевых агентов бота
6. **Ф8.9** — конверсия SC из мягкого в блокирующий

## 8. Источники

См. frontmatter `sources:` — 25 ключевых ссылок (arxiv 2024-2026, GitHub-проекты, industrial blogs).
