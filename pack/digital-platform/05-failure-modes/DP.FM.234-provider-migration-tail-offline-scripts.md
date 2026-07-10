---
id: DP.FM.234
name: "Provider migration tail — offline scripts stay on old provider"
name_ru: "Хвост миграции провайдера: offline-скрипты остаются на старом LLM-провайдере"
summary: "После миграции online-путей на новый LLM/API-провайдер offline-скрипты (ingestion, cron, переиндексация, инструменты разработчика) молча продолжают использовать старый провайдер, пока не будут запущены вручную."
type: fm
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: provider-migration
valid_from: 2026-06-22
related:
  see_also: [DP.FM.060]
tags: [provider-migration, offline-scripts, llm, openai, openrouter, migration-tail]
source: "WP-242 close, peer-09, report.md §3 bug, commit DS-my-strategy 4a58242c9, 2026-06-22"
schema_version: 1
---

# DP.FM.234 — Хвост миграции провайдера: offline-скрипты остаются на старом LLM/API-провайдере

## Описание

При смене LLM/API-провайдера (например, OpenAI → OpenRouter) online-пути мигрируют первыми и быстро выглядят работающими: запросы через бота, MCP-вызовы, live API — всё зелёное. Offline-пути (ingestion, cron-скрипты, инструменты разработчика, ретроспективный анализ) остаются на старом провайдере незамеченными — ошибка проявляется только при ручном запуске или случайной проверке.

**Причина:** тест «это работает» выполняется на live-path, который мигрирован. Offline-точки в CI не smoke-тестируются. Нет единого реестра всех мест использования провайдера.

## Симптомы

- Online-пути работают корректно после миграции
- При ручном запуске переиндексации / ingestion-скрипта — ошибка аутентификации старого провайдера или неожиданные расходы на старом аккаунте
- `grep -r "old_provider_client"` находит файлы, которые думали что уже мигрированы

## Detection test

«Является ли каждый скрипт/cron/воркер, использующий LLM, smoke-тестируемым в CI после миграции?»
- Нет → возможен DP.FM.234

## Prevention

1. **Pre-migration inventory:** `grep -r "old_provider_import\|old_api_key" --include="*.py" .` — собрать полный список точек использования перед миграцией.
2. **Post-migration smoke:** запустить не только live-path, но и все offline-точки (ingestion, переиндексация, cron) в smoke-режиме.
3. **CI coverage:** добавить проверку offline-скриптов как шаг CI после деплоя миграции.

## Связи

- DP.FM.060 (Half-migration manifest-runner split) — смежный класс неполной миграции в DB-контексте; данный — про LLM-провайдер с offline-tail.
