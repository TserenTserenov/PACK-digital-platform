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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Точность tuple-comparison (`(occurred_at, id) > ($1, $2)` устраняет потерю строк с равным timestamp) ↔ сложность cursor-схемы (композитный курсор хранит два поля вместо одного, усложняя debugging и саму cursor-таблицу) | Однозначная защита от потери событий стоит дополнительного состояния, которое нужно мигрировать и поддерживать в каждом projection-worker |
| Zero-downtime миграция (`ADD COLUMN IF NOT EXISTS` + fallback NULL→0) ↔ временная неоднородность поведения worker'ов | Пока не все инстансы worker'а перешли на композитный курсор, в системе одновременно работают старый (single-column) и новый (tuple) режимы — миграция безопасна для деплоя, но усложняет диагностику в переходный период |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Bulk-insert переоценивается как единственный триггер | Проблема описывает bulk-insert/high-rate как типичный кейс потери событий, но «Контекст применимости» указывает на более широкое условие — отсутствие stable secondary key в принципе; внимание практика фиксируется на явных bulk-insert инцидентах, недооценивая тихий drift при постоянном, но невысоком темпе с грубой гранулярностью `occurred_at` |
| Отсутствие ошибок снижает срочность диагностики | Симптом («`events_processed` < `events_emitted` без видимых ошибок») требует активного сравнения метрик; внимание команды съезжает к «нет алертов — значит всё в порядке», хотя именно бесшумность — сигнатурный признак этого класса бага, а не повод для спокойствия |

## Связи

- **Обобщает:** `feedback_silent_projection_fail.md` (28 апр 2026, описание симптома без формализованного решения).
- **Дополняет:** WP-274 per-domain cursor (изоляция между доменами) — это правило про корректность *внутри* одного домена.
- **Альтернатива:** transactional snapshot + WAL-replay — сложнее, но допускает re-processing.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
