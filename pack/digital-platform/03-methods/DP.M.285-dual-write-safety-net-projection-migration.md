---
id: DP.M.285
type: method
title: Dual-write safety net при миграции на projection-architecture
trust: confirmed
epistemic_stage: confirmed
domains: [projection-architecture, migration, event-sourcing, cqrs]
source_session: 2026-06-05 peer-session 10 WP-392 Б1
source_commit: gateway-mcp 950dc70, bot 8c357cc
valid_from: 2026-06-05
schema_version: 1
---

# DP.M.285 — Dual-write safety net при миграции на projection-architecture

## Контекст

Миграция source-of-truth с прямой записи (например, бот пишет напрямую в `traits.tier`) на projection-driven (worker пишет на основе доменного события).

## Проблема чистого cutover

Стоп старого write → запуск worker → мгновенное переключение **опасен**: worker может молча упасть, persisted state потеряет свежесть, fallback маскирует. См. DP.FM.135 (fallback-mask).

## Метод

1. **Внедрить projection-worker как canonical writer** (события источника → set field).
2. **Оставить старый прямой write как dual-write safety net** — идемпотентный (например, `set jsonb_value` не ломает повторяемость).
3. **После зелёного e2e и недели наблюдения** — откатить прямой write одним revert-коммитом.

## Tradeoff

На период stabilization у persisted state два писателя (техдолг). Это компромисс ради zero-downtime cutover.

## Тест применимости

«Миграция меняет writer одного и того же поля?» Да → dual-write период обязателен.

Анти-паттерн: чистый cutover без safety net → silent fail worker уничтожает freshness данных.

## Различение

- **Outbox pattern** — решает delivery guarantees (событие гарантированно опубликовано).
- **Dual-write safety net** — гарантирует freshness в переходный период миграции на projection.

Это разные задачи. Outbox остаётся на write-path; dual-write safety net убирается после стабилизации.

## Применимо к

- Миграции на event sourcing
- Projection-worker rollout
- Переход с monolith-write на CQRS-write
- Любая смена writer одного поля

## Связано

- DP.FM.135 — fallback-mask без бэкфилла (motivation)
- WP-270 (projection-worker)
- WP-392 Б1 — источник кейса
