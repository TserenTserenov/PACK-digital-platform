---
id: DP.M.054
name: Targeted backfill via dedicated queue for cursor-workers
type: method
domain: DP
status: active
valid_from: 2026-05-16
source: WP-310 Quick Close, 2026-05-16
related:
  - DP.M.028  # Stateless Worker Cursor Pattern (основа)
  - lesson: lessons_cursor_advance_vs_downstream_done  # проблема, которую решает
---

# DP.M.054 — Точечный бэкфилл через выделенную очередь (cursor-воркер)

## Назначение

Добавить N пользователей в downstream-функцию когда cursor projection-воркера уже продвинулся мимо соответствующих событий. Альтернатива cursor reset (side effects на весь поток) и полного replay событий (дорого, нарушает идемпотентность).

## Когда применять

1. Cursor воркера уже прошёл мимо событий (нет возможности переобработать)
2. Новая downstream-функция нужна только для subset пользователей (не для всего потока)
3. Replay нарушил бы идемпотентность других проекций, работающих с тем же потоком

## Механизм

Вместо cursor reset или replay — хирургическая вставка N строк в специализированную очередь обработки:

```sql
-- Пример: вставка задач для 5 wave-1 пилотов в guide_render_queue
INSERT INTO guide_render_queue (queue_id, user_id, context, created_at)
VALUES
  (4, '<uuid-1>', '{"trigger": "backfill", "source": "wave1"}', NOW()),
  (5, '<uuid-2>', '{"trigger": "backfill", "source": "wave1"}', NOW()),
  ...
ON CONFLICT (user_id) DO NOTHING;  -- идемпотентность
```

Воркер обрабатывает строки очереди как обычные задачи — изменения в коде не требуется.

## Инварианты

- **Идемпотентность:** ON CONFLICT DO NOTHING или UPSERT — повторный запуск не дублирует задачи
- **Хирургичность:** вставляются только строки для нужного subset, не перезаписывается cursor
- **Совместимость:** воркер-код не меняется — только данные в очереди

## Failure modes

- Вставка строк без idempotency guard → дублирование обработки
- Вставка для ALL users вместо subset → нарушение "хирургичности", нагрузка как при full replay
- Использование при отсутствии специализированной очереди → cursor reset как fallback (см. DP.M.028)

## Связи

- **Расширяет:** DP.M.028 — cursor pattern (stateless worker, cursor в БД)
- **Решает проблему из:** `memory/lessons_cursor_advance_vs_downstream_done.md`
