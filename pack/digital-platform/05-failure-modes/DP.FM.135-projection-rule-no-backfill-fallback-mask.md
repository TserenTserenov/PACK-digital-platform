---
id: DP.FM.135
type: failure-mode
title: Fallback-mask после новой projection-rule без бэкфилла существующих entities
trust: observed
epistemic_stage: confirmed
domains: [projection-architecture, event-sourcing, ddd, ops]
source_session: 2026-06-05 peer-session 10 WP-392 Б1 hermes-tier-bridge
source_commit: b264e5a (backfill-tier-to-persona.py)
related: [lessons_green_metric_masks_broken_loop.md]
valid_from: 2026-06-05
schema_version: 1
---

# DP.FM.135 — Fallback-mask после новой projection-rule без бэкфилла

## Симптом

Правило проекции `event → set field` добавляется **после** того как entities уже существуют в системе. Воркер обрабатывает только новые события — существующие entities значения поля не получают.

Read-path использует fallback (`field || default`) — выглядит как «работает», но фактически система не знает фактическое значение для existing rows.

- Новый пилот видит default-tier вместо своего реального
- Alerts молчат (нет ошибок)
- Root cause диагностируется только при попытке использовать «свежее значение»

Аналогичный класс ошибок: «green metric, broken loop» — отчёт PASS не означает что контур замкнут.

## Корень

Архитектурный пропуск этапа миграции: при добавлении projection-rule забыли бэкфилл. У existing entities нет события, которое бы триггерило установку поля. Read-path с fallback маскирует пустоту — наружу выглядит штатно.

## Профилактика

При добавлении проекции (DDD-projection, event-sourcing, derived JSONB-store):

1. **Развернуть worker** с новой projection-rule.
2. **Обязательный one-shot бэкфилл** (idempotent, dry-run по умолчанию) для existing entities из источника истины — записать поле напрямую или эмитировать synthetic event.
3. **Только потом активировать read-path** с расчётом на наличие значения.

Тест применимости: «добавляется ли правило проекции, которое раньше не существовало?» Да → бэкфилл обязателен.

## Применимо к

- Projection-worker архитектуры (DDD)
- Event sourcing с late-added projection
- Derived columns в JSONB-store
- Любые migration сценарии с «новое правило → старые данные»

## Связано

- [lessons_green_metric_masks_broken_loop.md](../../../../memory/lessons_green_metric_masks_broken_loop.md) — WP-397, 4 июня; схожий паттерн «green маскирует разомкнутый контур»
- WP-392 Б1 (hermes-tier-bridge) — источник
