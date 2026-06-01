---
id: DP.M.086
name: "Cheap idempotency: dedicated notification_log вместо ALTER TABLE column"
type: method
status: active
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-30
related:
  prevents: [DP.FM.045]
  see_also: [DP.M.054]
tags: [idempotency, notification, ddl-free, deduplication, outbox-lite, hot-table]
source: "WP-330 peer-session 2026-05-29-29 report-draft §2 Тема 2 КС2-2 (marathon_practice notification dedup)"
schema_version: 1
---

# DP.M.086 — Cheap idempotency: dedicated notification_log вместо ALTER TABLE column

## Суть метода

Дедуп side-effect'ов (notification, webhook, HTTP call) реализуется через **отдельную лог-таблицу** `notification_log(event_key TEXT UNIQUE)` с `INSERT ... ON CONFLICT DO NOTHING`, без добавления state-колонок на основную бизнес-таблицу.

Каждый side-effect получает composable event_key вида `f"{domain}:{user_id}:{nonce}"` (например, `marathon_practice:1234567:2026-05-30` для notification «отправь практику пользователю N в день D»). Перед side-effect — `SELECT ... WHERE event_key=?` (probe). После success — `INSERT event_key`.

## Когда применять

1. Need dedup только для side-effect (ответ на «уже сделали ли это?»), не для filtering/sorting/query.
2. Основная таблица — горячая (фоновый writer, миллионы строк, нельзя долго блокировать ALTER TABLE).
3. Появляется новый event-type, и не хочется DDL-ноиз на каждый новый case.
4. Event-key composable из существующих полей (user_id, scope, time-bucket).

## Когда НЕ применять

1. Нужны query вида «покажи все unsent items» / «покажи pending» — отдельная state-колонка с миграцией всё-таки нужна.
2. Need TTL/cleanup, и нет partitioning strategy для лог-таблицы (без cleanup лог растёт без границ).
3. Стек не поддерживает `ON CONFLICT DO NOTHING` (или эквивалент UPSERT-NO-OP) — теряется главное преимущество.

## Механизм

1. Один раз: `CREATE TABLE notification_log (event_key TEXT PRIMARY KEY, created_at TIMESTAMPTZ DEFAULT now())`.
2. На каждый side-effect:

   ```python
   key = f"{domain}:{user_id}:{nonce}"
   if was_notification_sent(key):  # SELECT EXISTS(...)
       return
   try:
       send_message(...)
   except Exception:
       raise  # НЕ writel log!
   else:
       try_insert_notification(key)  # INSERT ... ON CONFLICT DO NOTHING
   ```

3. Cleanup (опционально): cron / partition-drop по `created_at < now() - interval '30d'` для event-types с естественным TTL.

## Tradeoff

- **Плюсы:** zero миграций на основной таблице, composable event_keys, lock-free дедуп, deploy не блокирует hot table.
- **Минус:** «log ≠ state» — нельзя запросить «покажи все pending», только «is X done?». Для query — таки колонка.

## Композиция

- С DP.FM.045 (log-after-success): этот метод реализует remediation; INSERT log выполняется ТОЛЬКО при success side-effect.
- С outbox pattern: notification_log = «факт случился»; outbox-таблица = «факт нужно совершить» (planned/scheduled). Два разных контракта.

## Тест применимости

«Нужен ли мне dedup только для probe (yes/no), или ещё для filtering/sorting?»
- **Только probe** → notification_log (этот метод).
- **+ filtering/sorting** → state-колонка с миграцией.

## Антипаттерн

`ALTER TABLE main ADD COLUMN done_at` на каждый новый event-type → DDL-ноиз, миграции каскадом, lock-table на больших таблицах, deploy блокируется на ожидании ACCESS EXCLUSIVE lock.

## Связи

- **Prevents:** DP.FM.045 (log-after-success violation) — метод реализует корректный порядок log-after-success.
- **See also:** DP.M.054 (targeted-backfill-via-queue) — другой паттерн «без миграции основной таблицы».
- **Source:** WP-330 peer-session 2026-05-29-29 report-draft §2 Тема 2 КС2-2 (marathon_practice notification dedup, handlers/marathon.py).
