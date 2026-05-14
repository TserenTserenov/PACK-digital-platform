---
id: DP.M.017
name: Runtime Tool Discovery через JSON-RPC
name_ru: Метод динамического обнаружения инструментов MCP
name_en: Runtime Tool Discovery via JSON-RPC
type: method
status: draft
summary: "LLM-клиент строит список tool в runtime через tools/list JSON-RPC с TTL-кэшем (15 мин) и fallback на last-known-good при недоступности сервера. Hardcoded список tool = антипаттерн."
created: 2026-05-11
valid_from: 2026-05-11
trust:
  F: 3
  G: domain
  R: 0.90
epistemic_stage: evidence
related:
  realized_by: [DP.SC.129]
  references: [DP.SOTA.014]
tags: [mcp, tool-discovery, llm, client, json-rpc, caching, resilience]
source: "WP-5 #16 (IntegrationGate Ф1→Ф2), 2026-05-11"
---

# Runtime Tool Discovery через JSON-RPC (DP.M.017)

## Назначение

Метод определяет, как LLM-клиент должен получать список доступных инструментов (tools) от MCP-сервера — динамически, без hardcoded описаний в коде клиента.

Применим к любому LLM-клиенту, интегрирующему MCP-сервер с изменяемым каталогом capabilities.

## Алгоритм

### Bootstrap-загрузка (при старте клиента)

1. Отправить `tools/list` JSON-RPC к целевому MCP-серверу
2. Получить список `ToolDescriptor` с JSON-Schema
3. Применить tier-фильтрацию (naming convention: `admin_*`, `subscriber_*` или explicit tags в tool-descriptor)
4. Сохранить в local cache с TTL = 15 мин

### Периодическое обновление (TTL-refresh)

1. По истечению TTL — повторить bootstrap-загрузку
2. При успехе — обновить кэш, сбросить TTL
3. При ошибке — сохранить last-known-good, зафиксировать в лог

### Режим отказа (MCP-сервер недоступен)

1. Вернуть last-known-good кэш (max TTL: 1 hour)
2. Если кэш пуст → вернуть `essential: true` subset или пустой список
3. Не блокировать работу клиента из-за недоступности MCP

## 4 инварианта

1. **Клиент не знает схему tool заранее** — список строится только из ответа сервера
2. **Список запрашивается при каждом bootstrap** — не hardcoded при компиляции/деплое
3. **TTL-кэш снижает latency** — повторные обращения ≤ 500ms
4. **Fallback на last-known-good** — недоступность сервера не роняет клиента

## Антипаттерн

**Hardcoded список tool** в коде клиента:
- Не подхватывает новые tool на сервере без правки кода клиента
- Требует синхронного деплоя клиента и сервера
- Нарушает эволюционируемость L2 платформы (DP.ARCH.001)

## Связи

- **Сервисный контракт:** DP.SC.129 — конкретное обещание для бота и LLM-клиентов платформы
- **SOTA:** DP.SOTA.014 — MCP как де-факто стандарт 2026
