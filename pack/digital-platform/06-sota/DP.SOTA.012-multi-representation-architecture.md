---
id: DP.SOTA.012
name: Multi-Representation Knowledge Architecture
type: sota
status: active
summary: "Model/View эволюционировал в multi-representation: vector + graph + hierarchical index, отделённые от surface (бот, курс, API)"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: domain
  R: 0.7
related:
  integrates_with: [DP.SOTA.002, DP.SOTA.004]
  enables: [DP.D.018, DP.NAV.001]
  extends: [DP.ARCH.001]
tags: [model-view, multi-representation, knowledge-architecture, vector, graph, hierarchical]
---

# Multi-Representation Knowledge Architecture

## 1. Определение

**Multi-Representation Knowledge Architecture** — принцип проектирования knowledge systems, где одна модель (source-of-truth) порождает множество представлений (views) для разных потребителей и задач. Эволюция классического Model/View в контексте AI-native систем.

## 2. Статус SOTA (февраль 2026)

**SOTA, evolved paradigm.**

- **Autonomous knowledge runtimes (2026-2030):** множественные representations: vector embeddings (semantic search), knowledge graphs (relationship reasoning), hierarchical indexes (categorical navigation).
- **Модульные knowledge bases (2026):** компоненты можно swapать по мере эволюции технологий.
- **Принцип:** чёткое разделение между domain model (Pack/ontology/KG), retrieval layer, и surface (bot, course, API).

## 3. Три паттерна представлений

| Паттерн | Что | Пример в экзокортексе |
|---------|-----|----------------------|
| **viewOf(model)** | Проекция одной модели в другую форму | Pack entity → bot card, Pack → MCP response |
| **compositionViewOf(models[])** | Композиция из нескольких моделей | DayPlan = WeekPlan + commits + captures |
| **projectionView(model, concern)** | Подмножество по аспекту | Pack filtered by kind=SOTA → SOTA dashboard |

## 4. Правило различения Model vs View

- **Новая модель** = изменилась семантика, утверждения, границы, допущения
- **View** = та же семантика, другая форма/подача/потребитель

**Тест:** если изменение требует обновления Pack (source-of-truth) → это изменение модели. Если достаточно обновить downstream → это изменение view.

## 5. SPF-интеграция

| SPF-механизм | Multi-Rep аспект |
|-------------|------------------|
| Pack (source-of-truth) | Model: единственный источник знания |
| Downstream (bot, MCP, courses) | Views: проекции для разных потребителей |
| summary + frontmatter | Compressed view для retrieval |
| MAP (auto-generated) | Navigational view |
| ontology.md | Structural view (type hierarchy) |
| knowledge-mcp (pgvector) | Vector representation для semantic search |
| typed `related:` | Graph representation для traversal |

## 6. Правила применения

1. **Одна модель → много views.** Не дублировать знания в downstream. Проецировать.
2. **При добавлении нового surface** (бот, курс, API) — создавать view, не копировать Pack.
3. **Синхронизатор (DS-ai-systems/synchronizer)** — инструмент проекции, не копирования (DP.D.008).
4. **При проектировании MCP** — каждый tool = view над Pack: `pack_search` = semantic view, `pack_graph` = graph view, `pack_get` = full entity view.

## 7. Источники

- NStarX. «Next Frontier of RAG: Enterprise Knowledge Systems 2026-2030» (2026)
- BoldDesk. «Knowledge Base Architecture» (2026)
- SPF.SPEC.003 § 3-layer loading (= multi-representation protocol)
