---
id: DP.M.160
name: Single point of degradation tracking
type: method
trust: medium
epistemic_stage: practiced
status: active
created: 2026-05-23
sources:
  - session-close 2026-05-23 #8 (bot-error-fixes, Rule 5)
---

# DP.M.160: Single point of degradation tracking

## Принцип

Каждый наблюдаемый факт регистрируется в метрику/счётчик/event-log ровно **в одной точке** — там, где он наблюдается first-hand. Обёртки, observers, выше-уровневые wrappers становятся **чистыми наблюдателями** без побочных эффектов на ту же метрику.

## Пример нарушения (синтетический probe)

Дизайн с double-count:

```python
async def _api_call(...):
    try:
        response = await client.post(...)
        if response.status_code >= 500:
            record_api_degradation()  # точка 1
        return response
    except Exception:
        record_api_degradation()  # точка 1 (тот же inc)
        raise

async def _claude_health_probe():
    ok = await _api_call(...)
    if not ok:
        record_api_degradation()  # точка 2 — DOUBLE COUNT
```

Один реальный 5xx → +2 к counter → canary-ranking даёт false positive.

## Правильный дизайн

```python
async def _claude_health_probe():
    ok = await _api_call(...)
    # probe — чистый наблюдатель; degradation уже записан внутри _api_call
    if not ok:
        log_probe_failure()  # отдельная метрика «probe не прошёл», не та же
```

## Тест применимости

«Может ли один реальный факт быть посчитан N>1 раз?»
- Да → есть N−1 лишних точек учёта → нарушение принципа.
- Нет → каждая точка учёта уникальна по факту.

## Область применения

- Метрики (counters, gauges, histograms)
- Event-log / audit-log
- Canary-counters и health-probe ranking
- Cache invalidation (один write — один invalidate)
- Webhook fan-out (один внешний event — один внутренний event)

## Связи

- Failure mode: P-KE-002 (cross-context double-count) — типовое нарушение этого метода.
- DP.SOTA.* canary patterns (rule 5 family).
