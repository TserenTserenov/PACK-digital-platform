---
id: DP.FM.091
name: God-Table Anti-Pattern (склейка несвязанных доменов в core-таблице)
type: fm
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "WP-358 Ф10 ArchGate (2026-05-28), session-transcript 2026-05-28-08"
---

# DP.FM.091: God-Table Anti-Pattern

## Описание

Failure mode: core user-state таблица накапливает поля от несвязанных доменов — `marathon_state`, `active_session_id`, `onboarding_step` и т.д. в одной записи.

## Симптомы

- Хочется добавить tracking-поле в `user_state` для новой фичи
- Несколько компонентов/команд делают SELECT одной и той же core-таблицы
- ALTER TABLE на hot-table требует maintenance window

## Последствия

- **Cross-domain coupling:** сбой одного домена требует миграции таблицы, которую читают все
- **Latency:** каждый SELECT читает лишние поля чужих доменов
- **Fragility:** ALTER TABLE = рискованная операция при N параллельных читателях

## Детектор

«Хочу добавить поле в `user_state` — чей домен владеет этим полем?»
Если не тот же домен, что у core-таблицы → god-table anti-pattern активируется.

## Решение

Выделить domain-specific storage:
- FSM (для event-driven state machines, напр. aiogram FSMContext)
- Отдельная таблица с явным domain-owner
- KV-хранилище с domain-prefix

## Применимость

Любые реляционные БД с core-user-таблицей при росте приложения. Особенно критично в multi-tenant, event-driven и microservice архитектурах.
