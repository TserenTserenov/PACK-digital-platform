---
id: DP.FM.220
title: "DDL в health-check сериализует SQLite writers → ложный отказ под нагрузкой"
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform / observability
trust: draft
epistemic_stage: observed
valid_from: 2026-07-05
source: "session-close 2026-07-05 (WP-457 peer-сессия, тема llm-proxy.py)"
related:
  see_also: [DP.FM.194]
tags: [sqlite, health-check, ddl, concurrency, observability, side-effect]
---

# DP.FM.220 — DDL в health-check → ложный отказ под нагрузкой

## Описание

Исполнение DDL (`CREATE TABLE IF NOT EXISTS`) на горячем пути каждого запроса — в том числе в `/v1/health` — сериализует writers SQLite под конкурентной нагрузкой: health-check отвечает медленно, внешний watchdog ложно помечает сервис мёртвым, хотя процесс жив.

## Механизм

SQLite допускает только одного writer одновременно. При нескольких параллельных сессиях, одновременно инициирующих соединения с DDL, файл БД оказывается заблокированным. Если health-check попадает в это окно — он зависает или завершается ошибкой, хотя сам процесс-сервис продолжает работать.

## Признаки

- `_get_conn()` или аналог вызывает `CREATE TABLE IF NOT EXISTS` при каждом соединении
- Health-эндпоинт открывает соединение с БД
- Под нагрузкой (3+ параллельных сессий) health иногда отвечает медленно или с ошибкой
- Watchdog/launchd помечает сервис как dead при нормальной работе

## Тест

«Делает ли health-check боковой эффект или I/O в разделяемый ресурс?» Да → ложные отказы под нагрузкой.

## Контрмера

Два правила:
1. **DDL — один раз при старте:** создавать схему в `main()` до старта сервера (`_ensure_schema`), не в функции получения соединения.
2. **Health-check без I/O в БД:** health-эндпоинт проверяет живость процесса (pid-файл, in-memory флаг), не состояние БД.

## Связи

- DP.FM.194 (launchd stale pid) — смежный класс ложных отказов watchdog
