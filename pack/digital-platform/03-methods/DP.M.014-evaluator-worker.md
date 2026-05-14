---
id: DP.M.014
name: Evaluator Worker
kind: Method
status: draft
domain: digital-platform
type: architectural-pattern
created: 2026-05-08
source: WP-298, session-transcript 2026-05-08
---

# DP.M.014 Evaluator Worker

## Определение

**Evaluator Worker** — специализированный тип worker'а в activity-based системах, принимающий ДВА типа входных данных:
1. Сырые события за временное окно (domain_event, window 30d)
2. Уже вычисленные составные индексы (например, M-индексы из profiler)

Выход: derived classification state (например, stage_transitions).

## Отличие от Projection Worker

| Аспект | Projection Worker | Evaluator Worker |
|--------|-----------------|-----------------|
| Вход | Событийный поток (один тип) | Два типа: события + индексы |
| Выход | Проекция состояния | Классификация / ступень |
| Частота | Непрерывно / по событию | По расписанию (батч) |
| Зависимость | Независим | Зависит от предыдущего worker'а |

## Паттерн Timer-Chain

Evaluator не работает один — встраивается в timer-chain:

```
profiler (M-индексы) → evaluator (ступень) → rewards-handler (multiplier)
```

Каждый следующий зависит от предыдущего через БД, а не через прямой вызов.

## Single-replica контракт

Evaluator — stateless по 12-factor (F6), но при batch-расчётах требует single-replica enforcement, иначе разные replicas могут одновременно произвести разные stage_transitions для одного актора. Решение: `pg_try_advisory_lock` на shared БД как cheap enforcement (WP-307 Ф8, 12 мая 2026).

```sql
SELECT pg_try_advisory_lock(hashtext('stage_evaluator'));
-- TRUE → текущая replica становится owner; FALSE → выйти immediately
```

См. `memory/lessons_advisory_lock_single_replica.md`.

## Причина выделения в отдельный сервис

Разные частоты обновления входных данных:
- События поступают непрерывно
- Составные индексы считаются раз в сутки (батч)

## Применимость

Любая система activity-based прогресс-трекинга с составными индексами и многоступенчатой классификацией (геймификация, квалификация, обучение).

## Связи

- DP.M.015 (4-слойная каскадная зависимость) — надсистемный паттерн
- WP-298 — первая реализация (stage_evaluator.py)
- WP-214 — контекст ступеней мастерства
- WP-121 — контекст rewards-worker
- WP-307 — 12-factor audit (F6 stateless + advisory_lock)
