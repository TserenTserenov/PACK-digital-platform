---
id: DP.D.114
type: distinction
title: "Continuous data trend ≠ point-in-time measurement"
status: active
created: 2026-05-30
trust_level: T2
epistemic_stage: validated
domains: [observability, gate-design, measurement]
related:
  - DP.D.094
  - DP.M.224
---

# DP.D.114: Continuous data trend ≠ point-in-time measurement

## Различение

**Continuous data** — поток измерений одной величины во времени (cost/мес из Metabase, latency p95 за день, internal_metrics rows/день, count за неделю). Точка на графике через 1-2 месяца уже composite signal: содержит temporal context, реализована как тренд.

**Point-in-time measurement** — discrete snapshot, не имеющий встроенной temporal оси (snapshot опроса, А/Б-тест с фиксированной длительностью, milestone-snapshot калибра).

## Тест

«Source данных — continuous time-series или discrete snapshot?»

- Continuous → single trend analysis достаточна; требование двух последовательных measurements **избыточно** (удваивает срок до решения без выигрыша по достоверности).
- Discrete → двойной measurement как защита от шума **корректен** (point-in-time не несёт temporal context; replication обязательна).

## Следствия

1. **Gate-design для slow-changing systems.** При gating по continuous-метрикам (Prometheus, Metabase, internal_metrics) — single trend + early-warning trigger'ы на конкретных порогах. Не требовать «второй последовательный замер».
2. **Discrete A/B и снимки.** Замеры калибра, опросы пользователей, milestone-измерения — replicate ≥2 раза до решения.
3. **Триггеры orthogonal к main gate.** Конкретные пороги (cost >$50/мес, latency >10s) — early-warning без удвоения main gate.

## Антипаттерн

Применение «требовать ≥2 measurements» к continuous-метрикам → удваивает срок до разблокировки без выигрыша по точности; ломает observability-driven unblocking.

## Связи

- **DP.D.094** (temporal-correlation-vs-causation) — соседняя ось, но другой вопрос: «корреляция во времени = причина?». DP.D.114 — «достаточно ли одного temporal-измерения?».
- **DP.M.224** (delta-signal vs raw values) — метод применения различения в bi-weekly review.

## Источник

Peer-session 2026-05-30-24-fork3-wp-244-observability-unblock (Тема 5): обсуждение Развилки 4 (n≥30), где Metabase cost/мес как continuous metric не требует двух последовательных замеров — single trend + 4 trigger'а покрывают риск.