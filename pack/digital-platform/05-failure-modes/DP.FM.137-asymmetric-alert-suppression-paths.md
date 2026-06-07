---
id: DP.FM.137
type: failure-mode
title: Асимметричная suppression-логика в нескольких alert-путях — ложные тревоги
trust: observed
epistemic_stage: confirmed
domains: [monitoring, alerting, bot-infrastructure, event-driven]
source_session: 2026-06-06 session-close (WP-330 error suppression fix)
source_commit: 6c3b37c (db/queries/errors.py + core/scheduler.py)
valid_from: 2026-06-06
schema_version: 1
---

# DP.FM.137 — Асимметричная suppression-логика в alert-путях

## Симптом

Monitoring-система содержит два или более независимых пути до алерта. Только у части путей есть фильтр подавления (suppression) для benign-сценариев. Benign-ошибки (пользователь заблокировал бота, истёк токен, expected timeout) проходят через путь без suppression → генерируют алерты.

Дополнительный дефект: `logger.error()` вызывается ДО проверки `is_blocked()` → лог ошибки появляется даже при известном benign-сценарии.

## Корень

Alert paths добавлялись поэтапно. Suppression-логика добавлена только к «основному» пути, второй путь — legacy или добавлен позже — не получил ту же фильтрацию. Нет единой точки входа с общим suppression.

## Профилактика

Правило: **все** alert paths применяют одну и ту же suppression-логику.

Практические подходы:
1. **Single alert entry point** — вынести suppression выше по callstack, до любого `logger.error()`.
2. **Centralized suppression check** — shared функция `is_suppressed(error, context)` вызывается из каждого пути.
3. **Alert path audit** — при добавлении нового alert path: grep по коду «сколько мест могут вызвать alert?», проверить каждый на наличие suppression.

Тест: «Сколько мест в коде могут инициировать алерт пользователю/оператору? У всех ли одинаковый suppression-фильтр?» Более одного места без единой suppression → риск.

## Применимо к

- TG-боты с несколькими scheduler-задачами (check_errors + check_escalation)
- Monitoring-воркеры с несколькими путями (health + alerts + escalation)
- Любые системы с distributed alert generation

## Связано

- WP-330 (error suppression fix) — источник
