---
id: DP.M.174
name: "Triple-hash idempotency для LLM-pipeline"
type: method
pack: PACK-digital-platform
domain: llm-pipeline
trust: 0.80
epistemic_stage: established
valid_from: 2026-05-25
source: session-close-feed 2026-05-25 (WP-353 Ф1 commit c47f96be — cognitive-proxy-pipeline.py)
---

# DP.M.174 — Triple-hash idempotency для LLM-pipeline

## Суть

Кэширование результатов LLM-вызовов через композитный ключ `SHA-256(pilot_id | content_hash | schema_version)`. Cache hit → skip LLM. Изменение схемы оценки (`schema_v=v2`) автоматически инвалидирует old cache без ручной очистки.

## Зачем тройной хэш

| Двух-компонентный ключ (pilot \| content) | Тройной ключ (pilot \| content \| schema_v) |
|--------------------------------------------|----------------------------------------------|
| При изменении промпта/схемы оценки старый кэш синтаксически валиден, семантически устарел | Изменение `schema_v` автоматически разводит cache namespaces |
| Требуется ручная очистка cache при каждой ревизии prompt/schema | Smart invalidation — старые ключи не пересекаются с новыми |
| Риск отдать устаревшую оценку при rolling-deploy | Старый и новый pipeline работают параллельно без коллизий |

## IPO

| | |
|---|---|
| **Вход** | `pilot_id`, входной контент (текст), `schema_version` |
| **Процесс** | `key = sha256(f"{pilot_id}|{content_hash}|{schema_v}")` → lookup в cache → hit: return; miss: LLM-вызов → store |
| **Выход** | Кэшированная или свежая оценка |

## Алгоритм

1. `content_hash = sha256(canonicalized_input)` — нормализация (trim whitespace, sort keys в JSON).
2. `key = sha256(f"{pilot_id}|{content_hash}|{schema_version}")`.
3. Lookup → cache hit → вернуть.
4. Cache miss → LLM-вызов → store under `key` → вернуть.

## Когда нужно ревизовать schema_v

- Изменился prompt template (system / user).
- Изменилась output-schema (added/removed/renamed поля).
- Изменился scoring rubric или calibration table.
- Изменилась LLM-модель (если результат не модель-агностический).

## Тест применимости

«Изменил промпт — нужен ли пересчёт ранее закэшированных результатов?» Да → `schema_v` обязан быть частью cache-key. Нет (например, чистая retrieval без scoring) — двух-компонентного ключа достаточно.

## Аналог в других системах

- **DB migrations:** `schema_version` колонка → app знает, можно ли читать row напрямую или нужна upgrade-логика.
- **Browser cache:** ETag + Content-Type → новый Content-Type инвалидирует кэш.
- **Build artifacts:** hash(source) + compiler_version → пересборка при смене компилятора.

## Применимость

- LLM-classifier / scorer / summarizer с per-input выходом.
- Любой pipeline, где prompt/schema эволюционирует, а пересчёт всего корпуса дорог.
- Multi-tenant: добавление `pilot_id` в ключ изолирует кэши tenants.

## Связи

- **Применяется в:** cognitive-proxy-pipeline (DS-my-strategy, WP-353 Ф1).
- **Расширяет:** общий паттерн content-addressable storage схема-aware-ключом.
