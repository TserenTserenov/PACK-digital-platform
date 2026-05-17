---
id: DP.M.046
name: Keyset pagination для projection-worker
type: method
domain: data-pipeline
status: active
trust: validated
epistemic_stage: refined
valid_from: 2026-05-14
sources:
  - WP-310 Решение 1 (decision-log-2026-05.md commit f4104347)
  - DS-IT-systems/activity-hub/workers/stage_transition_listener.py
  - feedback_silent_projection_fail.md (28 апр 2026, обобщение)
related:
  - DP.FM.NNN-silent-projection-fail
  - WP-274 per-domain cursor (изоляция домена)
schema_version: 1
---

# DP.M.046 — Keyset pagination (occurred_at, id) для projection-worker

## Проблема

Projection-worker, использующий single-column high-water mark `WHERE occurred_at > $cursor`, теряет события когда несколько строк имеют идентичный `occurred_at` (типично при bulk-insert или high-rate scenarios). Worker запоминает максимальное `occurred_at` обработанного батча → следующий запрос пропускает хвостовые строки с тем же timestamp.

**Симптом:** «calculated value missing for some users after batch ingestion» — projection отстаёт на N событий, метрика `events_processed` < `events_emitted` без видимых ошибок.

## Решение: композитный keyset cursor

Заменить single-column cursor на tuple-comparison:

```sql
WHERE (occurred_at, id) > ($1, $2)
ORDER BY occurred_at, id
LIMIT $batch_size
```

Где `$1` — последний обработанный `occurred_at`, `$2` — последний обработанный `id` (UUID или sequential int). Tuple-comparison в Postgres детерминирован (лексикографический порядок: сначала по `occurred_at`, при равенстве — по `id`).

## Миграция cursor-таблицы (zero-downtime)

```sql
ALTER TABLE projection_cursors
  ADD COLUMN IF NOT EXISTS last_seen_id UUID;
```

- `IF NOT EXISTS` — идемпотентность.
- Worker'ы в полёте продолжают работать на single-column cursor; новый код читает `last_seen_id` (NULL → 0/'00000000-...').
- При первом проходе после деплоя cursor обновляется на композитный.

## Контекст применимости

- Применимо ко всем projection-worker'ам IWE: rewards, achievements, indicators, qualifications.
- Условие: source-таблица имеет stable secondary key (`id` обычно UUID или bigint sequential).
- Не нужно для worker'ов с unique-timestamp (single emitter, low rate).

## Связи

- **Обобщает:** `feedback_silent_projection_fail.md` (28 апр 2026, описание симптома без формализованного решения).
- **Дополняет:** WP-274 per-domain cursor (изоляция между доменами) — это правило про корректность *внутри* одного домена.
- **Альтернатива:** transactional snapshot + WAL-replay — сложнее, но допускает re-processing.
