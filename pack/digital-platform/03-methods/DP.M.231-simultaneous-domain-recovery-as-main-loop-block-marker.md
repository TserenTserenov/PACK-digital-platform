---
id: DP.M.231
name: "Одновременное восстановление N domain-rules как диагностический маркер блокировки main loop"
type: method
domain: event-driven-worker / diagnosis
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-29
schema_version: 1
source: "session-close 2026-05-29 (peer-session 2026-05-29-10-event-loop-stall-replay)"
---

# DP.M.231 Диагностика: одновременное восстановление всех domain-rules = main loop block

## Решение

В multi-domain projection worker паттерн одновременного падения/восстановления всех N domain rules — диагностический маркер блокировки главного корутина, а не изолированного domain-фейла.

## Таблица диагностики

| Наблюдение в логах | Диагноз |
|---|---|
| Все N rules упали и восстановились одновременно | Блок в main coroutine: `pool.acquire()` без timeout, бесконечный `wait_for` |
| Один домен упал, остальные живы | Domain-специфичная ошибка (SQL, schema, etc.) |

## Механизм

При блокировке main event loop все domains «замерзают» вместе. Восстановление происходит одновременно когда OS закрывает zombie TCP (OS keepalive ~7-10 мин). При изолированном domain-фейле восстанавливается только один домен.

## Применимость

- asyncio worker с несколькими независимыми доменами на общем event loop
- Работает по логам (не требует live-debug)
- Типичный период блокировки до OS keepalive: 7-10 мин
