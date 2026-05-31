---
id: DP.SC.165
name: Scope-control для bridge write-tools
type: sc
status: draft
layer: L2-Platform
summary: "Bridge write-tools (`personal_write`, `personal_propose_capture`) проходят server-side scope check в gateway-mcp; bridge cache TTL=60s даёт быстрый deny без round-trip"
consumer: R2 (DevOps платформы), пользователь Tier T2+ через VS Code bridge
created: 2026-05-31
updated: 2026-05-31
related:
  extends: [DP.IWE.005]
  uses: [DP.SC.163, DP.SC.036]
  produces: [запись в personal/*, agent_scope_violations row при deny]
---

# [DP.SC.165] Scope-control для bridge write-tools

> **Контекст:** WP-373 + WP-381. Bridge `iwe-gateway-bridge.py` (WP-200 Ф9) реализует 5 read-only proxied tools. Write-tools (`personal_write`, `personal_propose_capture`) были отложены без scope-контроля — это обещание заполняет gap. WP-381 добавляет peer-pilot continuation category (Claude Code / Kimi / GPT в IWE-среде пилота).

## Правило (инвариант)

- [ ] **Server-side enforcement** (Парламент-модель, HD): scope check для bridge write-tools живёт в `gateway-mcp` (CF Worker), НЕ в bridge. Bridge — client process, не trust boundary.
- [ ] **Single trust boundary**: все write-операции к backend MCP (`personal-knowledge-mcp`, `dt-mcp`, `knowledge-mcp`) проходят через один gate в `gateway-mcp`. Backend MCPs не дублируют scope check.
- [ ] **Defense-in-depth cache**: bridge держит in-memory cache scope (TTL=60s) для fast-path deny без HTTP round-trip. Cache hit на allow → всё равно server-side final check (cache hit на deny → можно отказать без round-trip). Server invalidate через `revoked_at` re-check на каждом call (даже при cache hit на bridge).
- [ ] **Per-tool identity**: каждый bridge tool имеет уникальный `agent_id` формата `iwe_bridge:<tool>` (`iwe_bridge:personal_write`, `iwe_bridge:personal_propose_capture`). Singleton `iwe_bridge` запрещён.
- [ ] **Peer-pilot continuation agents** (`pilot-helper-in-environment`, потенциально другие локальные CLI-агенты пилота) — отдельная категория без `:<role>` суффикса. Условия:
  - Только narrow scope: `personal-guide` source + `lesson/**` paths.
  - Только authenticated user-owned operations (UUID == repo owner, проверяется через `user_sources` lookup).
  - Path normalization обязательна (анти-traversal guard).
  - Missing `_meta.agent_id` + tool ∈ {`personal_write`, `personal_propose_capture`} → fallback на peer-pilot scope lookup (`agent_id='pilot-helper-in-environment'`).
  - Расширения категории (новые peer-pilot agent_ids) требуют ArchGate ЭМОГССБ профиля + явного PR в этот §27.
- [ ] **Discriminator schema**: расширение существующей `agent_scopes_mvp` через колонку `scope_kind text` (`server` | `bridge`); каждый lookup query обязан фильтровать `WHERE scope_kind = 'bridge'` для bridge enforcement (и `'server'` для agent-runner).
- [ ] **Audit log violations**: каждый отказ → row в `agent_scope_violations` с `request_id` (correlation с `proxy_calls`). Peer-pilot allow → log в `proxy_calls` с `agent_id='pilot-helper-in-environment'` + `bypass_reason='missing_declared_agent_id'` для post-incident analysis.
- [ ] **Deny-by-default**: пользователь без granted scope для `iwe_bridge:*` или `pilot-helper-in-environment` → 403 на любой write-call.

## Обещание

**Кому:** Bridge process в VS Code пилота (`iwe-gateway-bridge.py`), Ory JWT-аутентифицированный. Peer-pilot continuation agents — локальные CLI-агенты в IWE-среде пилота (Claude Code, Kimi, и т.д.).

**Зачем:** Бэкенду MCP-tools записать данные в `personal/*` пилота через MCP-прокси без риска, что произвольный агент с валидным JWT получит unscoped write-доступ.

**Что получит:**
- При allow: проброс call к target backend MCP, обычный response.
- При deny: HTTP 403 + MCP error body `{code: -32001, message: "scope denied", data: {reason: "path_not_allowed", attempted_path: "..."}}` + row в `agent_scope_violations` (Indicators DB).
- При cache miss и server unavailable: deny + retry-after header (fail-closed).

**Триггер:** Bridge вызывает write-tool через MCP (`personal_write` или `personal_propose_capture`) → gateway-mcp принимает HTTP POST → JWT verify → scope lookup → allow/deny.

**Время отклика:**
- Bridge cache hit (deny): <1ms.
- Server-side scope check (cache miss или 60s expiry): +20-80ms к baseline tool call latency (один Neon lookup).
- Deny audit log insert: async, не блокирует ответ.

**Режим отказа:**

| Ситуация | Поведение |
|----------|-----------|
| Ory JWT невалиден | 401, не доходит до scope check |
| Нет subscription | 403 Subscription required (тот же контур что DP.SC.163) |
| `agent_id` не из whitelist `iwe_bridge:*` и не peer-pilot fallback | 400 Bad Request + audit `invalid_agent_id` |
| `agent_scopes_mvp` lookup пуст | 403 deny-by-default + violation `scope_not_found` |
| `scope.revoked_at NOT NULL` или `expires_at < now()` | 403 + violation `scope_expired` / `scope_revoked` |
| `attempted_path` не матчит `allowed_paths` глоб-pattern | 403 + violation `path_not_allowed` |
| Peer-pilot path traversal (`lesson/../.claude`) | 403 + violation `path_not_allowed` после `normalizePath` |
| Peer-pilot source != `personal-guide` | 403 + violation `source_not_allowed` |
| Indicators DB unreachable | 503 + retry-after; bridge cache **не** используется как fallback (fail-closed) |

## Whitelist (WP-381)

| agent_id | Категория | Trigger detection | Allowed source | Allowed paths | Allowed operations |
|---|---|---|---|---|---|
| `iwe_bridge:personal_write` | Bridge-tool | Explicit `_meta.agent_id` в MCP envelope | `*` (scope row) | `*` (scope row) | `write` |
| `iwe_bridge:personal_propose_capture` | Bridge-tool | Explicit `_meta.agent_id` | `*` (scope row) | `*` (scope row) | `propose` |
| `pilot-helper-in-environment` | Peer-pilot continuation | Missing `_meta.agent_id` + Bearer subject == repo owner | `personal-guide` | `lesson/**` | `write`, `propose` |

## Свидетельства

**Данные:**

| Критерий | Как проверить |
|----------|--------------|
| Колонки `scope_kind` + `allowed_operations` в `agent_scopes_mvp` | `\d agent_scopes_mvp` в Indicators DB; CHECK на `scope_kind IN ('server','bridge')` |
| Bridge cache реализован | `personal_write` после второго deny идёт без HTTP round-trip (timing) |
| Whitelist `agent_id` в gateway | `curl https://gateway-mcp.../mcp -d '{...agent_id: "evil"}' → 400` |
| Violation log пишется | `SELECT count(*) FROM agent_scope_violations WHERE agent_id LIKE 'iwe_bridge:%'` после deny smoke |
| Peer-pilot scope rows exist | `SELECT count(*) FROM agent_scopes_mvp WHERE agent_id='pilot-helper-in-environment' AND scope_kind='bridge'` |
| Peer-pilot path traversal blocked | `personal_write(source="personal-guide", path="lesson/../.claude/secrets")` → 403 |

**Контекст:**

| Условие | Проверка |
|---------|---------|
| Пилот установил bridge через connect-guide | `~/.iwe/iwe-bridge-venv/bin/python iwe-gateway-bridge.py` running |
| В `agent_scopes_mvp` есть row `(agent_id='iwe_bridge:personal_write', scope_kind='bridge', user_id=$pilot)` | `SELECT ...` |
| Peer-pilot row существует | `SELECT ... WHERE agent_id='pilot-helper-in-environment' AND user_id=$pilot` |
| Tier T2+ (та же гарантия что DP.SC.163) | subscription check в gateway-mcp |

**Полномочия:**

| Роль | Что подтверждает |
|------|-----------------|
| Owner Role: R2 (DevOps платформы) | Деплой gateway-mcp с scope.ts + миграцию `agent_scopes_mvp` |
| Ory | Валидность JWT, `user_id` claim |
| Neon Indicators DB | Lookup `agent_scopes_mvp` |

**Свидетельства:**

| Свидетельство | Источник |
|--------------|---------|
| Allow → запись в target backend MCP | `personal-knowledge-mcp` log / `dt-mcp` log |
| Deny → row в `agent_scope_violations` | `SELECT ... WHERE request_id = $X` |

## Архитектура (вкратце)

```
[VS Code: Claude Code / Kimi / GPT]
    │
    │ MCP call: personal_write(source, path, content)
    │   ├─ explicit _meta.agent_id = iwe_bridge:personal_write → bridge-tool path
    │   └─ missing _meta.agent_id → peer-pilot fallback (WP-381)
    ▼
[iwe-gateway-bridge.py]
    │ 1. Bridge cache lookup (TTL=60s)
    │    ├─ hit:deny → 403 без HTTP (fast deny)
    │    └─ hit:allow или miss → продолжаем
    │
    │ 2. HTTPS POST к gateway-mcp + Ory JWT
    ▼
[gateway-mcp (CF Worker)]
    │ 1. JWT verify (existing)
    │ 2. Subscription check (existing)
    │ 3. **Scope check**
    │    ├─ explicit agent_id → whitelist iwe_bridge:* → DB lookup
    │    ├─ missing agent_id + tool ∈ {personal_write, personal_propose_capture}
    │    │    → peer-pilot fallback:
    │    │       a. normalizePath(path) startsWith "lesson/"
    │    │       b. source == "personal-guide"
    │    │       c. user_sources lookup (cache TTL=5min)
    │    │       d. DB lookup agent_id='pilot-helper-in-environment'
    │    ├─ deny → INSERT agent_scope_violations + return 403
    │    └─ allow → продолжаем
    │ 4. Fan-out к target backend MCP (personal-knowledge-mcp / dt-mcp / knowledge-mcp)
    ▼
[Backend MCP] → response → bridge → Claude Code
```

## Реализующие сервисы (MAP.002)

| Сервис | Роль | Триггер |
|--------|------|---------|
| iwe-gateway-bridge | R29 / IWE-bridge | MCP call from Claude Code |
| gateway-mcp | R2 / scope-enforcer | HTTPS POST from bridge |
| Neon Indicators DB | data | scope lookup, violation insert |
| Neon persona DB | data | `user_sources` lookup (WP-381) |
| backend MCPs (personal-knowledge-mcp, dt-mcp, knowledge-mcp) | provider | proxied tool calls |

## Migration path

Текущая реализация: расширение `agent_scopes_mvp` (см. DP.SC.163 § Migration path). При миграции на Keto — bridge scope rows становятся отдельным policy generator (отличается от server-agents):
- Server rows: `(user, agent_id, repo)` triples — `owner/repo` semantics.
- Bridge rows: `(user, agent_id, source_name, operation)` triples — IWE source semantics.
- Peer-pilot rows: `(user, agent_id='pilot-helper-in-environment', source='personal-guide', path='lesson/**')` — narrow scope.

`scope_kind` column остаётся для аудита 90 дней после Keto cutover, затем drop вместе со всей `agent_scopes_mvp`.

## Связь с другими обещаниями

- **Extends:** [DP.IWE.005] — Local Gateway architecture. Bridge — это infrastructure для local-MCP делегации в cloud.
- **Uses:** [DP.SC.163] — Server-agents через Gateway: переиспользует таблицу `agent_scopes_mvp` через discriminator pattern.
- **Uses:** [DP.SC.036] — Routing Gate: gateway-mcp проверяет scope ДО fan-out.

## Открытые вопросы (не для MVP WP-373 / WP-381)

- **Revoke UI**: где пилот может revoke scope для конкретного bridge tool? Сейчас — manual UPDATE в Neon. Production UX → отдельный РП после Keto.
- **Per-tool rate-limit**: rate limit стоит на gateway tools globally; per-tool с учётом scope — не в MVP.
- **Audit log retention**: 90 дней для `agent_scope_violations` (как proxy_calls)? Открыт — отложено в WP-373-tail.
- **Bridge cache invalidation**: пуш-сигнал от server при revoke (через SSE или re-poll). Сейчас 60s TTL компромисс.
