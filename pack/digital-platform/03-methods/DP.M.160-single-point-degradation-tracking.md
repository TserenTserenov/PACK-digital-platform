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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Полнота покрытия ошибочных путей ↔ единственность учёта одного факта | Каждая обёртка «на всякий случай» вызывает `record_api_degradation()`, и один реальный 5xx даёт +2 к counter; метод требует ровно одной точки записи — там, где факт наблюдается first-hand, а обёртки делает чистыми наблюдателями без побочных эффектов на ту же метрику |
| Локальная простота обёртки ↔ глобальная корректность метрики | Строка `record_api_degradation()` в `_claude_health_probe` пишется мгновенно, но ломает canary-ranking false positive'ом; корректность достигается разведением метрик — probe пишет свой `log_probe_failure()`, не трогая чужой счётчик |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Покрытие всех веток ошибок затмевает двойной счёт | Практик проверяет, что каждый путь отказа записан, и не проверяет обратное — не записан ли один факт дважды на разных уровнях обёрток; тест «может ли один факт быть посчитан N>1 раз» остаётся неприменённым |
| Рост счётчика читается без вопроса о числе точек записи | Внимание на значении метрики и срабатывании canary-ranking, а не на количестве мест в коде, которые её инкрементируют — ложная тревога разбирается как «нестабильный API», а не как дефект учёта |

## Связи

- Failure mode: P-KE-002 (cross-context double-count) — типовое нарушение этого метода.
- DP.SOTA.* canary patterns (rule 5 family).

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
