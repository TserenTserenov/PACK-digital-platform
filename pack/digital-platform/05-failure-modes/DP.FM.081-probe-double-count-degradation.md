---
id: DP.FM.081
name: "Double-count в probe-пути: одно событие → два инкремента деградации"
type: failure-mode
pack: PACK-digital-platform
domain: observability
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-23
source: peer-session 2026-05-23-07-bot-errors-afternoon §2.5
---

# DP.FM.081 — Double-count в probe-пути: одно событие → два инкремента деградации

## Описание

Failure mode гибридных probe-функций, использующих инструментированный API-клиент. Конструкция:
1. `health_check()` вызывает `_api_call` (который уже инкрементирует счётчик деградации через свой error-handler при 5xx).
2. Затем `health_check()` ещё раз вызывает `record_api_degradation()` при ошибочном результате.

Один сбой → 2 инкремента → canary триггерится в 3× раньше → spurious пауза scheduler'а.

## Симптом

- Canary паузирует на одиночных событиях вместо порога (3 события).
- Rate-of-errors метрика показывает 2× выше реальных.
- Scheduler без явных ошибок раз в час уходит в pause.

## Причина

Probe-функция не имеет boundary с инструментированным API-клиентом. Оба пишут в один и тот же счётчик деградации.

## Фикс

Probe в системе с автоматическими счётчиками деградации:
- **Вариант A:** probe обходит инструментированный путь (вызывает raw HTTP, не клиент).
- **Вариант B:** probe отключает counter в этом пути через context-флаг (`with degradation_counter_disabled():`).

## Тест-детектор

В коде: вызов `_api_call(...)` внутри `health_check()` И вызов `record_api_degradation(...)` после него в той же функции = double-count smell.

## Применимость

Любая система canary/circuit-breaker с автоматическим degradation counter и health probe, использующим инструментированный клиент.

## Связь

- DP.M.168 (post-deploy регрессия) — этот FM был обнаружен через post-deploy hypothesis.
- DP.D.093 (метка ≠ источник) — `[scheduler/L1]` маскировал реальный источник double-count.
