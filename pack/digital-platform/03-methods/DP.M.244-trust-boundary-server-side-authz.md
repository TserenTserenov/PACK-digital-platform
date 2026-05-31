---
id: DP.M.244
title: Trust Boundary Server-Side Enforcement — авторизация в gateway, не в клиенте
type: method
domains: [security, gateway, multi-agent, authorization]
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-31
source: WP-373 (scope-control), DP.SC.165
pack_refs:
  - DP.D.NNN (Парламент-модель — архитектурный контекст)
---

# DP.M.244 — Trust Boundary Server-Side Enforcement для multi-client gateway

## Суть

Авторизационная проверка (scope check, permission gate) живёт на стороне сервера (gateway), а **не** в клиенте (bridge/agent-process). Bridge — недоверенный клиент.

## Почему клиент не является trust boundary

1. Bridge-процесс может быть скомпрометирован или модифицирован.
2. N клиентов = N enforcement точек → drift → уязвимость.
3. Ревокация scope требует рестарта всех клиентов вместо одного изменения на сервере.

## Паттерн реализации

```
Gateway (server) → authoritative scope check на каждый вызов
Bridge (client)  → in-memory deny cache (TTL=60s, max=256 entries)
                   fast-path reject без round-trip при known-deny
                   НЕ является заменой серверной проверки
```

## Правило Defense-in-Depth

- **Primary gate:** gateway проверяет scope при каждом tool-call.
- **Cache:** bridge держит результаты deny (только deny!) TTL=60s. Cache снижает latency, но не заменяет серверную проверку.
- **Revoke path:** сервер помечает `revoked_at` → bridge cache истекает через TTL → следующий call → 403.

## Применимость

Любой multi-client gateway с centralized authz: MCP gateway, OAuth resource server, API gateway с RBAC.

## Прецеденты

- WP-373: `iwe-gateway-bridge.py` + `gateway-mcp` scope enforcement.
- DP.SC.165: bridge-scopes MVP с server-side check.
