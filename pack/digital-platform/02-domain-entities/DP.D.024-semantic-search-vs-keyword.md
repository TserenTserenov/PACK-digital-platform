---
id: DP.D.024
name: "Semantic Search ≠ Keyword Search"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-16
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.024: Semantic Search ≠ Keyword Search

| | Semantic (Vector) Search | Keyword Search |
|---|---|---|
| Что ищет | Смысл (embedding similarity) | Точную строку / слова |
| Хорош для | Естественный язык, «похожие документы» | Коды (DP.ROLE.001), точные термины |
| Слаб в | Структурированные идентификаторы, acronyms | Переформулировки, синонимы |
| Скорость | ~300ms (embedding + vector scan) | ~5ms (GIN index) |
| Модель | bge-m3 → 1024d cosine distance | pg_trgm ILIKE + tsvector FTS |

**Почему важно**: knowledge-mcp обслуживает и людей (смысловые вопросы), и AI-агентов (tool_use по кодам сущностей). Один тип поиска не покрывает оба случая. Hybrid retrieval — стандарт production RAG 2025 (SOTA: Hybrid Retrieval dense+BM25).

**Тест**: Запрос содержит паттерн `XX.YYY.NNN` (код сущности)? → keyword path. Запрос на естественном языке? → vector path. Непонятно? → оба (RRF).

**Реализация:** knowledge-mcp v3.1 — keyword-first routing + vector fallback. Схема: pg_trgm GIN + tsvector GIN. Без изменения MCP API.
