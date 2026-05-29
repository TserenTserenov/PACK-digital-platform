---
id: DP.FM.094
name: "Бинарный счётчик advance маскирует легитимные причины non-advance (DLQ-blocked)"
type: fm
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "session-transcript 2026-05-28 (peer-сессия bot-fixes-review)"
---

# DP.FM.094: Binary Counter Masks Legitimate Non-Advance

## Описание

Счётчик «событий без advance» не различает два случая: (A) advance пустой из-за ошибки → CRITICAL; (B) advance пустой из-за DLQ-block всех доменов → штатный режим ожидания. Метрика одна, семантика разная.

## Симптомы

- Все domains заблокированы DLQ → `advanced == set()` на каждом событии
- `events_without_advance` инкрементируется N раз (по числу событий)
- CRITICAL-лог при N > threshold, хотя worker функционирует штатно
- PagerDuty / алерт при штатном режиме ожидания

## Корневая причина

Бинарное разделение success/failure не покрывает case «штатное ожидание». Счётчик не знает о контексте DLQ-blocked — он просто видит пустой advance.

## Последствия

- Ложные CRITICAL-алерты → alert fatigue → игнорирование реальных CRITICAL
- Операционный шум при каждом DLQ-timeout

## Решение

Разделить счётчик по причине non-advance:
```python
# В return-значении process_event:
return ProcessResult(
    advanced=...,
    blocked_count=len(blocked_domains)
)

# В мониторинге:
if advanced == set() and result.blocked_count == 0:
    events_without_advance.inc()  # только реальный CRITICAL
```

## Обобщение

**Паттерн:** бинарный счётчик успех/неуспех маскирует легитимные причины «неуспеха». При проектировании метрик: перечислить все причины «неуспеха» — есть ли среди них штатные? Если да → разделить счётчик по причине.

## Применимость

Любой event-driven worker с projection cursors, DLQ и monitoring-счётчиком advance/no-advance.
