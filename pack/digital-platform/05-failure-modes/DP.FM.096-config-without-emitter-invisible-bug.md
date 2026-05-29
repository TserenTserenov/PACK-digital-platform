---
id: DP.FM.096
type: failure-mode
name: Config without emitter — invisible zero events
pack: PACK-digital-platform
domain: digital-platform
trust: high
epistemic_stage: validated
valid_from: 2026-05-28
source: session-transcript 2026-05-28, WP-327 v4.4 apply (G4 incident)
---

# DP.FM.096 — Config without emitter — invisible zero events

## Описание

Правило или конфигурация триггера зарегистрировано в системе (видно в UI), однако ни один producer не эмитирует соответствующий `event_type`. Система работает без ошибок, счётчик событий равен нулю. Пользователь видит правила — ничего не происходит.

## Контекст возникновения

Event-driven архитектуры с отдельными таблицами конфигурации (reward_rules, trigger_config) и потоком domain_event. Правило добавляется в конфигурацию без проверки наличия эмиттера в коде. Нет ошибок, нет логов — только нулевой результат.

## Детектор

При добавлении нового `event_type` в таблицу правил — обязательная проверка:

```bash
grep -r "<event_type>" ~/IWE/ --include="*.py"
```

Если эмиттер не найден → зафиксировать в context-файле РП:
`event_type X добавлен в rules, эмиттер TBD в Phase N`.

## Применимость

Любая конфигурация триггера (reward rules, notification rules, webhook rules) без парной реализации кода-эмиттера. Обобщение: любая конфигурация триггера без реализующего кода = invisible bug.

## Связи

- Урок: `memory/lessons_wp327_v44_apply.md`
- Паттерн: [DP.M.095-event-gateway-owner-integrity](../03-methods/) — single writer
