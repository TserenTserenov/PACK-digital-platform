---
id: DP.D.284
name: "`--allowedTools` в headless Claude Code принимает только полные namespaced имена MCP-инструментов"
type: distinction
domain: digital-platform / claude-code / mcp-configuration
status: draft
valid_from: 2026-07-22
sources:
  - "session-transcript, triage 2026-07-22 batch8"
tags: [claude-code, mcp, allowedtools, namespaced-name, headless]
---

# DP.D.284 — `--allowedTools` требует полное namespaced имя MCP-инструмента

## Суть

Флаг `--allowedTools` в headless-режиме Claude Code принимает **только полное namespaced имя** MCP-инструмента (например `mcp__server-name__tool_name`), а не короткое имя, под которым инструмент может быть известен по документации или другим контекстам (например `dt_read_digital_twin`).

## Тест

«Указано ли имя инструмента в `--allowedTools` в полной namespaced форме, соответствующей регистрации в Claude Code, или в коротком/документационном виде?» Короткое имя — не сматчится, инструмент останется недоступным без явной ошибки, объясняющей причину.

## Пример

`--allowedTools` получал короткое имя `dt_read_digital_twin`, а Claude Code регистрирует инструмент под полным namespaced именем (например `mcp__iwe-local-gateway__dt_read_digital_twin`) — короткое имя не матчилось. Headless-вызов без TTY не может интерактивно запросить разрешение, поэтому инструмент оставался просто недоступным.

## Почему важно различать

Headless-режим не даёт интерактивной обратной связи о том, что инструмент не подошёл под фильтр `--allowedTools` — отказ выглядит как «инструмент недоступен» без явного объяснения, что причина в формате имени, а не в отсутствии MCP-сервера.

## Связи

- Применимо к любой headless-конфигурации Claude Code с явным списком разрешённых MCP-инструментов
