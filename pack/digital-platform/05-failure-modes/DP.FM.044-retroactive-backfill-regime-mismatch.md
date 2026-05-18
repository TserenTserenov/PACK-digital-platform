---
id: DP.FM.044
title: Retroactive Backfill Regime Mismatch
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-17
valid_from: 2026-05-17
sources:
  - WP-311 Ф-Close commit b9179051 (29481 applied_events, 1148 effective>0 = 3.9%)
  - DP.ECON.001 v1→v2 transition
related:
  refines: []
  references: [DP.SC.137]
---

# DP.FM.044 — Retroactive Backfill Regime Mismatch

## Описание

При retroactive backfill событий (полный replay events за audit-gap окно) **высокий cap-truncation rate (>50%, в пределе >96%)** интерпретируется как баг matcher/reward_rules, но на самом деле — **сигнал regime change**: события начислены в старом режиме правил, и при re-application новых правил большинство account'ов уже превысили daily/weekly cap → новые delta = 0.

**Не баг.** Сигнатура успешного post-cutover backfill при изменившихся cap'ах.

## Триггер

- Cut-over к новой версии rewards/правил/cap'ов (например, DP.ECON.001 v1 → v2).
- Retroactive backfill historical events через новый matcher.
- Sampling cap_truncation rate на результатах.

## Симптомы

- 29481 applied_events backfilled, но `effective > 0` только у 3.9% (1148).
- Большинство account'ов: balance не изменился или +small delta.
- Метрика «effective_event_rate» резко падает на historical-окне.

## False-positive интерпретация (анти-паттерн)

> «Low effective-yield в backfill = bug в matcher/reward_rules.»

Investigation тратится на reward_rules debug, хотя cap-truncation объяснима через regime change.

## Правило (корректная интерпретация)

При cap_truncation rate >50% в historical backfill — **проверить hypothesis «cap уже исчерпан в старом режиме» ДО объявления incident'а**:

1. Sampling 10 accounts с `effective=0`.
2. Для каждого: `balance_at_event_time` ≥ `cap_for_period`? Да → regime change confirmed.
3. Если confirmed — backfill корректен, метрика отражает legacy саturation, не bug.

## Применимость

- Monitoring backfill jobs (DP.SC.136 rewards-transparency, DP.SC.137 rewards-analytics).
- Alerter rules для historical replay: cap_truncation_rate >50% → INFO, не WARN (если есть active cut-over).
- Post-cutover audit (DoD): обязательная панель cap-truncation breakdown, не сводный effective rate.

## Тест применимости (для FM-каталога)

«Backfill вернул effective=0 на N% событий?» Если N→100%:

- Был ли cut-over rewards в окне backfill? Да → DP.FM.044 (regime mismatch), investigation в matcher не нужна.
- Нет → проверить matcher (это уже другая FM — например, dedup-slice false-positive DP.FM.041).

## Связи

- DP.SC.137 rewards-analytics — обязательная панель cap-truncation rate.
- DP.ECON.001 — points-engine versioning (источник regime change).
