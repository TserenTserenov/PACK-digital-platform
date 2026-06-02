---
id: DP.FM.110
name: Unix socket без protocol handshake → пустой ответ
type: failure-mode
domain: digital-platform
prefix: DP
trust: evidence-based
epistemic_stage: established
valid_from: 2026-05-29
source: DS-my-strategy/inbox/captures.md (session WP-357, 2026-05-29)
---

# DP.FM.110 — Unix socket без protocol handshake → пустой ответ

## Описание

`nc -U /path/to.sock` отправляет raw bytes без protocol-уровневого согласования. Structured protocol (MCP JSON-RPC, LSP, RESP) требует `initialize` handshake перед ответом. Без handshake сервер молчит → `resp=''` → код интерпретирует как «недоступен» вместо «неправильный протокол».

## Симптом

Socket файл существует, nc подключается успешно, данные не приходят. Exit code 2 (или timeout). False-negative: сервер жив, но выглядит как мёртвый.

## Механизм

```
nc -U → raw bytes → structured protocol сервер ждёт initialize sequence → тишина → resp='' → false-negative
```

## Исправление

Минимальный клиент с правильным protocol handshake. Для MCP:

```python
# scripts/lib/gateway-lock.py — Python MCP-клиент с JSON-RPC initialize sequence
```

## Применимость

Любой structured protocol (MCP, LSP, RESP, Redis RESP). Тест: «требует ли протокол setup-фазу перед обменом данными?» Да → голый `nc -U` недостаточен.

## Связи

- Связан с DP.FM.105 (internal-probe): оба о silent failure в probe-сценарии, разные механизмы