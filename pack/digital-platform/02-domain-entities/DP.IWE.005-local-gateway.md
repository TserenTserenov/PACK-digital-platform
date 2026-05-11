---
id: DP.IWE.005
name: Local MCP Gateway (in-process multi-agent layer)
type: domain-entity
status: draft
created: 2026-05-11
updated: 2026-05-11
trust:
  F: 2
  G: domain
  R: 0.6
epistemic_stage: design
related:
  specializes: [U.System]
  uses: [DP.IWE.001, DP.IWE.002]
  realized_by: [DP.SC.034, DP.SC.035]
  distinct_from: [DP.IWE.003]
wp: WP-150 Ф6
---

# Local MCP Gateway (DP.IWE.005)

## 1. Определение

**Local MCP Gateway** — паттерн in-process / Unix-socket слоя внутри VS Code, через который peer-агенты (Claude Code, Kimikode и др.) маршрутизируют MCP tool-вызовы и координируют запись в файлы workspace через pessimistic-lock протокол.

В отличие от облачного Gateway-паттерна (DP.IWE.003), Local Gateway:
- **Single-user, multi-agent** (один пользователь, ≥2 агента в одной сессии)
- **In-process / local socket** (не HTTPS)
- **Эфемерное состояние** (locks теряются при перезапуске процесса)
- **Без авторизации** (доверяет всем процессам с доступом к сокету)

## 2. Различение: Local Gateway ≠ Cloud Gateway (Aisystant MCP)

| Характеристика | Cloud Gateway (DP.IWE.003) | Local Gateway (DP.IWE.005) |
|---|---|---|
| **Инстанс** | `mcp.aisystant.com` | `/tmp/iwe-local-gateway.sock` (или аналог) |
| **Потребитель** | Внешний AI-клиент (claude.ai, ChatGPT, Cursor) | Peer-агент в VS Code (Claude Code, Kimikode) |
| **Транспорт** | HTTPS + OAuth (Ory) | Unix socket / TCP localhost |
| **Tenancy** | Multi-tenant (RLS по user_id) | Single-user, multi-agent |
| **Главное обещание** | Доставка знаний/ЦД с access control | Tool routing + write conflict prevention |
| **Conflict detection** | Не нужно (один клиент за раз) | Критично (≥2 агента в одном workspace) |
| **Lifetime** | Постоянный сервис | Per-session (стартует с VS Code) |
| **Pack-сущность** | DP.IWE.003 (cloud) | DP.IWE.005 (local) |
| **Service Clauses** | DP.SC.021, DP.SC.023, DP.SC.129 | DP.SC.034, DP.SC.035 |

**Tест на путаницу:** если задача про «как пользователь подключает claude.ai к платформенным знаниям» — это Cloud Gateway. Если задача про «как два агента в моей VS Code не наступают друг другу на ноги» — это Local Gateway. Документы Pack не должны смешивать эти два паттерна в одном файле.

## 3. Архитектура

```
VS Code workspace (один пользователь)
├── Claude Code  ──┐
├── Kimikode    ──┤   MCP JSON-RPC через Unix socket
└── другие peer ──┘
         ↓
    iwe-local-gateway (отдельный процесс)
    ├── Tool router (allowlist per agent identity)
    ├── Lock manager (file-level pessimistic locks)
    ├── Shared metrics (tool_call_count, lock_collision)
    └── Upstream proxy (опционально → Aisystant MCP для cloud tool)
         ↓ для local tool                  ↓ для upstream tool
    file/git/terminal                  HTTPS → mcp.aisystant.com
```

**Ключевые компоненты:**

- **Tool router** — детерминированно отдаёт peer-агенту только разрешённый allowlist при `tools/list`. Allowlist хранится в `~/.iwe/gateway-config.yaml`.
- **Lock manager** — in-memory dictionary `{file_path: {holder_agent, acquired_at, ttl}}`. Pessimistic lock: agent → `acquire_file_lock` → `write_file` → `release_file_lock`.
- **Upstream proxy** — если peer-агент запросил tool из upstream-набора (например, `knowledge_search` на Aisystant MCP) — Gateway проксирует, кешируя identity (gateway-machine-identity, не end-user identity).

## 4. Lifecycle (per VS Code session)

| Фаза | Событие |
|------|---------|
| **Start** | VS Code открыт → задача-bootstrap запускает `iwe-local-gateway` (или auto-start при первом MCP connection) |
| **Active** | Peer-агенты подключаются через `initialize`, вызывают tools/list, делают tool_call с lock-протоколом |
| **Idle** | Все агенты отключились → процесс остаётся live с пустыми locks |
| **Shutdown** | VS Code закрыт / явная команда `iwe-local-gateway stop` → state теряется (locks эфемерны) |

## 5. Различение Gateway vs sync-протокол

Local Gateway отвечает только за **транспорт и lock**. **Что именно делать** агентам с lock'ами (порядок запросов, ожидание, переключение задач) — это **peer-agent choreography** (DP.SC.035 / DP.ROLE.039), отдельный слой поверх Gateway.

Тест: если правило про «как агент использует lock API» → choreography. Если правило про «что Gateway гарантирует при tool_call» → DP.IWE.005.

## 6. Связь с принципами DP.ARCH.001

| Принцип | Как реализуется |
|---|---|
| #6 Отчуждаемость | Gateway процесс — артефакт пользователя, можно остановить / переустановить |
| #11 Per-user blast radius | Никто кроме процессов одного пользователя не дотягивается до сокета (filesystem perms) |
| #13 ИИ-системы UI-agnostic | Любой MCP-совместимый агент может подключиться |
| #15 Multi-surface | Расширяет surface'ы IWE с «один агент» до «несколько peer-агентов в одной сессии» |

## 7. Безопасность (для §Б ArchGate чеклиста)

- **Транспорт:** Unix socket с permissions `0600` (rw только владельцу). TCP localhost — допустим только с loopback-bind.
- **PII:** Local Gateway не пишет PII в свой лог (логирует только tool name + agent + duration). Если upstream-proxy к Aisystant MCP — PII остаётся в upstream, Local Gateway не сохраняет ответ.
- **Authentication:** между peer-агентами и Gateway — отсутствует (доверие filesystem). Между Gateway и upstream — agent's credentials передаются прозрачно (Gateway = MITM по дизайну, но в trusted boundary одного пользователя).
- **Tier-фильтрация:** не Local Gateway (это про single-user). Tier — в upstream (DP.ROLE.038 MCP Tool Consumer).
- **STRIDE quick:** Spoofing — нет (filesystem trust). Tampering — нет (in-memory state). Repudiation — лог tool-вызовов. DoS — TTL на lock + max-concurrent-agents config. Disclosure — perms 0600. Elevation — не применимо.

## 8. Связанные документы

- [DP.IWE.001](DP.IWE.001-intelligent-working-environment.md) — IWE как концепция
- [DP.IWE.002](DP.IWE.002-iwe-template-and-setup.md) — шаблон и setup
- [DP.IWE.003](DP.IWE.003-gateway-architecture.md) — **Cloud Gateway (Aisystant MCP)** — отдельный паттерн, не путать
- [DP.SC.034](../08-service-clauses/DP.SC.034-local-mcp-gateway.md) — обещание Local Gateway
- [DP.SC.035](../08-service-clauses/DP.SC.035-peer-agent-choreography.md) — turn-based choreography поверх Gateway
- [DP.ROLE.039](DP.ROLE.039-peer-agent.md) — роль peer-агента

## 9. Открытые вопросы (для реализации)

1. Implementation language: Node.js (как gateway-mcp) или Go/Rust для in-process speed? — рекомендация Node.js для консистентности с другими MCP реализациями.
2. Discovery механизм auto-start: VS Code extension hook vs launchd plist vs PATH-shim? — отдельная сессия после первой ручной интеграции.
3. Config schema: где живёт `gateway-config.yaml` (allowlist per agent identity)? — `~/.iwe/gateway-config.yaml`, шаблон в `FMT-exocortex-template`.
4. Telemetry: писать ли метрики в файл / отправлять в platform? — локально в `~/.iwe/gateway.log`, опционально через capture-bus.
