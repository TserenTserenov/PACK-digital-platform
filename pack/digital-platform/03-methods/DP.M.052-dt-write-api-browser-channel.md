---
id: DP.M.052
title: "DT Write API как Транзитный Канал для Браузерных Инструментов"
type: method
pack: PACK-digital-platform
domain: digital-platform
tags: [digital-twin, browser-channel, mcp, channel-parity, db-access]
valid_from: 2026-05-16
status: active
---

# DP.M.052 — DT Write API как Транзитный Канал для Браузерных Инструментов

## Определение

Паттерн использования `dt_write_digital_twin` (MCP tool) как транзитного слоя для инструментов, которым нужна запись в БД, но недоступен прямой DB-доступ (браузерный канал claude.ai).

## Проблема

Инструмент работает в VS Code (есть CLI), но не в браузере (нет прямого Neon-доступа). Прямая запись в БД возможна только из VS Code.

## Решение

Браузерная версия инструмента использует `dt_write_digital_twin` (IWE MCP tool):

```
Браузер → dt_write_digital_twin → Digital Twin → DT-worker → Neon
```

Вместо:
```
VS Code → psycopg2/asyncpg → Neon (прямо)
```

## Ключевые свойства

- **Транзитность:** DT API = промежуточный буфер, DT-worker синхронизирует в Neon асинхронно
- **Channel-parity:** пилот в браузере получает тот же функционал, что в VS Code
- **Decoupling:** инструмент не знает о схеме БД — только о DT-контракте

## Тест применимости

«Инструмент пишет в БД, но нужен в браузере?» → использовать DT write API как транзит.

## Связи

Аналогично: DP.M.038 (idempotent-skill-distribution) — распределение артефактов через render для channel-parity.

## Источник

WP-318 Ф7 (2026-05-16), session-transcript + git diff DS-my-strategy (782793b3).
