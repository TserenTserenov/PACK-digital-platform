---
id: DP.SOTA.004
name: GraphRAG + Knowledge Graphs
type: sota
status: active
summary: "Комбинация vector search + knowledge graph traversal для multi-hop reasoning: 87% vs 23% accuracy по сравнению с базовым RAG"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.7
related:
  integrates_with: [DP.SOTA.002, DP.SOTA.012, DP.SYS.001]
  extends: [DP.NAV.001]
tags: [graphrag, knowledge-graph, vector-search, retrieval, multi-hop, ontology]
---

# GraphRAG + Knowledge Graphs

## 1. Определение

**GraphRAG** — метод retrieval, комбинирующий vector search (семантическая близость) с knowledge graph traversal (навигация по связям) для ответов на вопросы, требующие multi-hop reasoning. Знание организуется в граф (узлы = сущности, рёбра = типизированные связи).

## 2. Статус SOTA (февраль 2026)

**SOTA, production-ready.**

- **Accuracy:** 87% на multi-hop задачах vs 23% у базового RAG (Fluree, 2026).
- **Production:** Enterprise deployments в Walmart, eBay, финтех.
- **LightRAG** (HKUDS, EMNLP 2025) — lightweight вариант: граф из typed relations в frontmatter, без тяжёлой инфраструктуры.

## 3. SPF-интеграция

Pack уже содержит элементы GraphRAG:

| Pack-механизм | GraphRAG-аналог |
|---------------|-----------------|
| Typed `related:` в frontmatter | Рёбра графа (produces, uses, fails_with) |
| Entity Index в manifest | Индекс узлов |
| summary в frontmatter | Embeddings для vector search |
| MAP (auto-generated) | Graph visualization |

**Gap:** Нет traversal engine — сейчас навигация ручная. knowledge-mcp с pgvector — шаг к замыканию.

## 4. Правила применения

1. **При создании entity** — ВСЕГДА заполнять typed `related:`. Каждая связь = ребро графа.
2. **При проектировании MCP** — добавлять `pack_graph(id, depth)` tool для traversal по связям.
3. **Не использовать Full GraphRAG (Microsoft)** для структурированных Pack — избыточен. LightRAG через frontmatter достаточен.
4. **При >100 сущностей** — рассмотреть pgvector + graph traversal в knowledge-mcp.

## 5. Связь с экзокортексом

knowledge-mcp уже имеет pgvector для vector search. Следующий шаг: добавить graph traversal по `related:` полям для multi-hop запросов (e.g., «какие failure modes связаны с методами, которые использует бот?»).

## 6. Источники

- Microsoft Research. GraphRAG (2024)
- HKUDS. LightRAG (EMNLP, 2025)
- Fluree. «GraphRAG: Making Data AI-Ready for 2026» (2026)
- SPF.SPEC.003 § SOTA-методы (LightRAG, Hybrid Retrieval)
