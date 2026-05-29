---
id: DP.FM.093
name: "Retry storm guard создаёт orphaned content при деградации API в момент первой попытки"
type: fm
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "session-transcript 2026-05-28 (peer-сессия bot-fixes-review)"
---

# DP.FM.093: Retry Storm Guard — Silent Orphan

## Описание

Guard `if is_api_degraded(): return` в retry-функции защищает от retry storm, но создаёт «тихую дыру»: задача, упавшая в момент начала деградации API, не попадает ни в оригинал, ни в retry.

## Симптомы

- API деградировал именно во время первой попытки выполнения задачи
- Guard в `_schedule_retry` обнаруживает деградацию → немедленный return
- Retry job не создан
- Когда API восстановится — ни один механизм не «помнит» об этой задаче
- Контент / пользователь потерян до следующего daily cycle

## Корневая причина

Guard проектировался для защиты от каскадного retry при длительной деградации. Но он не различает два случая:
- задача уже была в retry-очереди (guard оправдан — не множить)
- задача только что упала и ещё не попала в retry (guard создаёт orphan)

## Последствия

- Silent потеря задачи — нет ни exception, ни warning, ни отчёта
- Пользователь не получает контент до следующего цикла (или вообще)

## Решение

**Вариант A:** reschedule на `paused_until + N_min` вместо skip:
```
if is_api_degraded():
    reschedule(task, delay=api_resume_estimate + 5min)
    return
```

**Вариант B:** recovery sweep — при восстановлении API запускать sweep пользователей с пустым pending-контентом.

**Тест паттерна:** «Что происходит с задачей, которая упала точно в момент начала деградации?»

## Применимость

Любой retry guard с is_degraded-проверкой при первичном вызове (не только повторе).
