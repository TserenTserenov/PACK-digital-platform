---
id: DP.M.376
type: method
title: source-scoped-candidate-pool-retrieval — source-scope фильтр + расширенный pool перед trim до TOP_K
kind: Method
pack: PACK-digital-platform
domain: digital-platform / retrieval-patterns
trust: observed
epistemic_stage: confirmed
domains: [vector-search, retrieval, knowledge-base, rag, source-filtering]
source_session: 2026-07-07 session-close (WP-149 Ф-v3-ссылки, prefetch-knowledge-snapshot.py)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: []
---

# DP.M.376 — Source-scoped candidate pool для векторного поиска

## Определение

При фиксированном page size векторного поиска по смешанной базе знаний — minority-source вытесняется из результатов более популярными источниками. Паттерн исправляет: явный `source=` фильтр + расширенный pool → preference-sort по верифицированному критерию → trim до TOP_K.

## Проблема (без паттерна)

Unscoped запрос с `limit=K` конкурирует со ВСЕЙ базой знаний. Minority-source (напр. `docs-courses`) вытесняется мажоритарными источниками (Pack-заметки, session logs, ecosystem docs). При TOP_K=5 и minority-доле < 10%: 0-2 кандидата из нужного источника.

**Тест:** «кандидаты из целевого источника в top-K?» Нет → применить паттерн.

## Алгоритм

```
1. source = <целевой источник> (явный, не default-all)
2. limit = POOL_SIZE (> TOP_K, напр. POOL_SIZE=10 при TOP_K=5)
3. Из пула → sort: сначала кандидаты с верифицированным атрибутом (guide_url, doi, etc.)
4. Trim до TOP_K из отсортированного пула
```

## Числа из инцидента

- До: unscoped, limit=5 → 0-2 верифицированных ссылки из 9-result run
- После: source=docs-courses, limit=10, prefer guide_url-verified → 8/9 (верифицировано live)

## Когда применять

- RAG/retrieval pipeline с несколькими типами источников
- Целевой источник — minority в общем объёме базы
- Есть верифицируемый атрибут качества кандидата (url, doi, pack-id)
- page-size фиксирован (API-ограничение или latency-бюджет)

## Ограничения

- Требует поддержки `source=` фильтра в retrieval API (knowledge-mcp поддерживает)
- POOL_SIZE > TOP_K добавляет N-K лишних retrieval vecs — незначительные для TOP_K ≤ 10

## Связано

Паттерн ортогонален методам query rewriting и HyDE (те улучшают запрос; этот — управляет пулом кандидатов).
