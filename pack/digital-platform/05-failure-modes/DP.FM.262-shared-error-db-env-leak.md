---
id: DP.FM.262
type: failure-mode
title: Shared error DB для pilot и prod без env-тега → алерты описывают чужой инцидент
trust: observed
epistemic_stage: confirmed
domains: [multi-env, monitoring, alerting, ops, staging-isolation]
source_session: 2026-07-07 session-close (peer-session 2026-07-07-01-l3-restart-auth-failure)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.FM.138]
---

# DP.FM.262 — Shared error DB без env-тега → алерты описывают чужой инцидент

## Симптом

Тестовая и боевая версии бота пишут ошибки в одну общую таблицу логов/алертов без `env` / `bot_id` тега. Ночные уведомления описывают ошибку pilot-инстанса под именем prod (или наоборот). Диагностика ведёт не туда: traceback есть, но он принадлежит другой среде.

Реальный кейс (WP-454, peer-session 2026-07-07-01): ночной алерт описывал L3-restart auth failure pilot-бота как production инцидент.

## Корень

Тест/pilot-инстанс использует тот же error log endpoint, что и production. Без env-метки все ошибки выглядят как prod incidents.

## Профилактика

**Правило:** каждый инстанс (pilot/prod/staging) должен иметь изолированный канал записи ошибок и алертов — либо отдельную таблицу, либо обязательный `env` / `bot_id` тег во всех записях.

Тест: «Могут ли алерты двух сред пересечься в одной очереди без env-признака?» Да → разделить изоляцией или обязательным env-тегом.

## Применимо к

- Telegram-боты с pilot и production инстансами, пишущие в shared Postgres
- Observability pipelines без tenant/env isolation

## Связано

- DP.FM.138 — общий паттерн shared БД без discriminator (функциональные данные); этот FM — специфика monitoring/alerting канала
