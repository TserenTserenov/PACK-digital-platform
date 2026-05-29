---
id: DP.FM.101
name: Rule-engine NOOP при отсутствии записи — silent event drop
type: fm
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "session-transcript 2026-05-28 (Quick Close WP-327 — диагностика клубных событий) + commit fcd77442"
---

# DP.FM.101: Rule-engine NOOP при отсутствии записи

## Описание

Rule-driven event processor (projection worker, pricing engine, access policy engine) при отсутствии соответствующей записи в справочнике правил молча игнорирует событие — не возвращает ошибку, не пишет в DLQ, не логирует WARNING.

## Симптомы

- События в event_store появляются, баллы/проекции не начисляются
- Нет алертов, нет ошибок в логах
- Проявляется только при ручном анализе «почему не начислилось»

## Последствия

- **Silent data loss:** события обработаны как NOOP без трассировки
- **Трудная диагностика:** нет записей об обработке — нет отправной точки

## Условие возникновения

Добавление нового типа события в source-системе без синхронного добавления записи в rule-table.

## Детектор

«Счётчик `unknownEventType` = 0, но новые `event_type` появляются» → Silent NOOP активен.

## Решение

При обработке события — сначала проверить наличие правила в справочнике:
1. Если правила нет → явный `WARN`-log с `event_type` + инкремент `unknownEventType`
2. Опционально — fallback record «обработано как unclassified»
3. DLQ-маршрут для неизвестных событий

## Применимость

Любой engine по принципу «lookup → apply»: gamification, ACL, pricing, routing, projection workers.
