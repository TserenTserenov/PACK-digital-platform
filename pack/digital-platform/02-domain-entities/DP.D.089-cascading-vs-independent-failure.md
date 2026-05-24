---
id: DP.D.089
name: "Cascading failure ≠ Independent failures"
type: distinction
status: active
created: 2026-05-23
source: session-transcript 2026-05-23-02-test-aist-bot-lesson (peer-сессия с Kimi)
trust: high
epistemic_stage: established
---

# DP.D.089 — Cascading failure ≠ Independent failures

## Различение

**Cascading failure** — один root cause, распространение по графу зависимостей, timeline coherent.
**Independent failures** — разные root cause, не пересекающиеся timeline, разные хосты/классы исключений.

## Признаки каскада

1. Timeline монотонно расширяется по компонентам (upstream → downstream).
2. Есть shared upstream dependency.
3. Симптомы становятся «громче» с временем (одна ошибка → волна → деградация UI).

## Признаки independence

1. Разные хосты.
2. Разные классы исключений.
3. Не пересекающиеся timeline (окна не соседствуют).

## Тест

«Если убрать предполагаемую root cause — пропадут ли все симптомы?»
- Да → каскад. Triage = 1 инцидент.
- Нет → независимые failure. Triage = N инцидентов.

## Анти-пример

2026-05-23 инцидент aist_bot: писатель в ход 0 классифицировал 6 симптомов как «два независимых потока». Реально — единый каскад через Claude API → lesson pipeline → /start handler → feed digest. Цена ошибки классификации: неверная decomposition на 2 параллельных RCA вместо одного.

## Применимо

- Incident triage (1 инцидент vs N).
- Root cause analysis (где искать root cause — в shared dependency или в каждом сервисе отдельно).
- Blast radius assessment.
- On-call decision (paging strategy).

## Связи

- DP.SOTA.030 (canary error rate) — детектирует ранний симптом каскада.
- AS.FM.032 (data-first-architecture-second) — без grep данных каскад спутается с independence.
