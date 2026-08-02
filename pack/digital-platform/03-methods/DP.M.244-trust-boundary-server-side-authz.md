---
id: DP.M.244
title: Trust Boundary Server-Side Enforcement — авторизация в gateway, не в клиенте
type: method
domains: [security, gateway, multi-agent, authorization]
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-31
last_updated: 2026-08-01
source: WP-373 (scope-control), DP.SC.165
pack_refs:
  - DP.D.NNN (Парламент-модель — архитектурный контекст)
---

# DP.M.244 — Trust Boundary Server-Side Enforcement для multi-client gateway

## Суть

Авторизационная проверка (scope check, permission gate) живёт на стороне сервера (gateway), а **не** в клиенте (bridge/agent-process). Bridge — недоверенный клиент.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Round-trip latency vs. centralization | Каждый tool-call требует серверной scope-проверки, что добавляет latency; метод жертвует скоростью ради единого авторитетного gate, исключая N точек enforcement на клиенте |
| Оперативная простота клиента vs. авторитет сервера | Bridge хочет локальный deny-cache, чтобы избежать round-trip; метод ограничивает кэш только known-deny и TTL, сохраняя сервер в качестве основного gate |
| Скорость отзыва scope vs. TTL кэша | Отзыв scope на сервере происходит мгновенно, но устаревший кэш клиента может пропускать вызовы до истечения TTL; метод балансирует скорость отзыва с TTL кэша |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Client-cache optimism | Практикующий воспринимает bridge deny-cache как достаточную оптимизацию и сокращает серверную проверку до fallback, воспроизводя N-point enforcement drift, который метод запрещает |
| Latency-driven centralization erosion | Под нагрузкой внимание смещается на сокращение round-trip; серверная scope-проверка обходится для "доверенных" внутренних клиентов, нарушая trust boundary |

## Применимость

Любой multi-client gateway с centralized authz: MCP gateway, OAuth resource server, API gateway с RBAC.

## Прецеденты

- WP-373: `iwe-gateway-bridge.py` + `gateway-mcp` scope enforcement.
- DP.SC.165: bridge-scopes MVP с server-side check.

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
