---
id: DP.SC.163
name: Серверные агенты через Gateway (MVP)
type: sc
status: draft
layer: L2-Platform
summary: "Пользователь через Gateway получает результат работы агента (Стратег, Экстрактор) в виде коммита в свой GitHub-репозиторий — без локального CLI, с тем же артефактом, что и через VS Code"
consumer: R1 Стратег, R15 Знание-Экстрактор (через делегацию), пользователь Tier T2+
created: 2026-05-29
updated: 2026-05-29
related:
  extends: [DP.IWE.003]
  uses: [DP.SC.034, DP.SC.036]
  produces: [GitHub commit в personal-guide]
---

# [DP.SC.163] Серверные агенты через Gateway (MVP)

> **MVP-skoп:** два агента — `run_strategist` и `run_extractor`. `run_synchronizer` отложен (двунаправленная запись — отдельная зона риска). См. peer-сессию `sessions/2026-05/2026-05-29-29-rhetoric-miner-archgate-runner/report.md`.

## Правило (инвариант)

- [ ] **Паритет с CLI** (DP.IWE.003 §8.2): результат серверного агента = результат локального запуска того же агента из VS Code. Артефакты в тех же репозиториях, в том же формате, с теми же метаданными. «Те же артефакты в тех же репо», НЕ «те же MCP-tools».
- [ ] **Прозрачный прокси** (Gateway, ArchGate 5 апр): Gateway пробрасывает MCP-вызов as-is, не агрегирует и не интерпретирует. Orchestration — на стороне agent-runner.
- [ ] **Deny-by-default scopes** (AS.M.001 §3.2.1): каждый агент имеет явный список разрешённых путей (`agent_scopes_mvp`). Запись вне списка → отказ + audit-event.
- [ ] **Per-user identity**: каждый вызов авторизован через Ory JWT, `user_id` сквозной от Gateway до GitHub commit.
- [ ] **Учёт стоимости**: LLM-расход агента → `proxy_calls` (WP-200), привязан к `user_id` для лимита по тарифу.

## Обещание

**Кому:** Пользователь Aisystant T2+ через claude.ai / любой MCP-клиент

**Зачем:** Получить результат работы агента (анализ, экстракция, коммит) без локального CLI, ноутбука, git. Tier T4 (полный экзокортекс) доступен с телефона через mobile-friendly MCP-клиент.

**Что получит:**
- **Коммит в репозиторий `personal-guide`** или другой репо в `agent_scopes_mvp.allowed_repos`, через GitHub App `aisystant-knowledge` (user-to-server flow).
- **Отчёт в чат** (claude.ai) с резюме: что сделал агент, какие файлы изменены, ссылка на коммит.
- **Запись в `proxy_calls`** с `user_id`, `cost_usd`, `latency_ms` — для биллинга.

**Триггер:** Вызов одного из трёх MCP-tools через Gateway:
- `run_strategist(user_prompt, context_artifacts?, idempotency_key?)` — async kick-off, возвращает 202 + run_id.
- `run_extractor(user_prompt, context_artifacts?, idempotency_key?)` — async kick-off, возвращает 202 + run_id.
- `get_run_status(run_id)` — sync GET, возвращает status (started/completed/failed/timeout) + error_code при failure + committed_files + result_url при completed.

**Async pattern (Ф3.5):** POST endpoints возвращают сразу 202 + `Location: /v1/runs/{run_id}`. Клиент опрашивает `get_run_status` каждые 5-10с до status != "started". `background_task_timeout_s = 200` хард-лимит на agent-runner; gateway side `idempotency_key` fallback = SHA-256 от (agent + user_prompt + context_artifacts) если клиент не передал явно — стабильный retry без дублей run.

**Время отклика:**
- `run_strategist`: 30-90 секунд (полный strategic analysis с записью в Strategy.md).
- `run_extractor`: 20-60 секунд (один capture-candidate из переданного текста).
- `get_run_status`: <100ms (DB lookup).

**Режим отказа:**
| Ситуация | Поведение |
|----------|-----------|
| Ory JWT невалиден | 401 Unauthorized, не запускаем агента |
| Нет активной подписки пользователя | 403 Subscription required, audit-event `agent_call_denied_no_subscription` |
| Превышен daily cost cap (WP-200 Ф6) | 429 Quota exceeded, audit-event `agent_call_denied_quota` |
| Запись вне `agent_scopes_mvp` | 403 Scope violation, audit-event `agent_scope_violation` |
| GitHub App установлен не на целевой репо | 502 GitHub install missing, инструкция пользователю установить через connect-guide |
| LLM-вызов через прокси упал | retry 2× с exponential backoff, после — 502 + сохранение partial state в `agent_run_state` |

## Свидетельства

**Данные:**

| Критерий | Как проверить |
|----------|--------------|
| MCP tool `run_strategist` зарегистрирован в Gateway | `curl https://gateway.aisystant.app/.well-known/mcp \| jq '.tools[].name' \| grep run_strategist` |
| Таблица `agent_scopes_mvp` существует в Neon | `SELECT count(*) FROM agent_scopes_mvp` |
| GitHub App `aisystant-knowledge` имеет permission `contents:write` | GitHub Settings → Installed Apps → aisystant-knowledge |

**Контекст:**

| Условие | Проверка |
|---------|---------|
| Пользователь установил `aisystant-knowledge` App на `personal-guide` | `github_status` MCP tool возвращает `app_installed: true` |
| Пользователь Tier T2+ | `subscription.contract WHERE account_id = $user_id AND tier >= 'T2'` |

**Полномочия:**

| Роль | Что подтверждает |
|------|-----------------|
| Owner Role: R2 (DevOps платформы) | Развёртывание agent-runner и регистрацию MCP-tool в Gateway |
| Ory | Валидность JWT и принадлежность `user_id` |
| GitHub | Успешность коммита (commit SHA в ответе) |

**Свидетельства:**

| Свидетельство | Источник |
|--------------|---------|
| Коммит в `personal-guide` от bot-аккаунта `aisystant-knowledge[bot]` | `git log --author='aisystant-knowledge\[bot\]'` |
| Запись в `proxy_calls` с `user_id` и моделью | `SELECT * FROM proxy_calls WHERE user_id = $X ORDER BY ts DESC` |
| Запись в `agent_run_log` (Neon) | `SELECT * FROM agent_run_log WHERE run_id = $Y` |

## Архитектура (вкратце)

```
[claude.ai / MCP-клиент] ──MCP──> [Gateway / Aisystant MCP]
                                       │
                                       │ run_strategist(params) + Ory JWT
                                       ▼
                                  [agent-runner]
                                       │
                                       ├──> LLM Proxy (WP-200) ──> Anthropic API
                                       │     (model по verification_class)
                                       │
                                       ├──> Neon: agent_scopes_mvp lookup
                                       │
                                       └──> GitHub App `aisystant-knowledge`
                                              └──> commit в personal-guide
```

Gateway = stateless прозрачный прокси. agent-runner = новый сервис (FastAPI / Cloudflare Worker), stateless workers + Neon для state (lock, cursor, scopes).

## Реализующие сервисы (MAP.002)

| Сервис | Роль | Триггер |
|--------|------|---------|
| agent-runner | R2 / agent-orchestrator | MCP call from Gateway |
| Gateway | R2 | MCP протокол |
| LLM Proxy (WP-200) | R2 | HTTP request from agent-runner |
| GitHub App `aisystant-knowledge` | внешний | git push |

## Пользовательский путь

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Установить `aisystant-knowledge` App на `personal-guide` | пользователь | connect-guide |
| 2 | Webhook `installation_repositories.added` → автоматически: индексация (user_sources) + auto-scope (agent_scopes_mvp) | платформа | gateway-mcp |
| 3 | Открыть claude.ai, вызвать `run_strategist({user_prompt: ...})` | пользователь | claude.ai + Gateway |
| 4 | Gateway → agent-runner → LLM-цикл → enforce_scope → commit | агент-раннер | agent-runner |
| 5 | Опросить `get_run_status(run_id)` каждые 5-10с до status=completed | claude.ai | Gateway |
| 6 | Получить `result_url = https://github.com/<repo>/commit/<sha>` в чате | пользователь | claude.ai |

## Связь с другими обещаниями

- **Extends:** [DP.IWE.003] — Cloud Gateway. Этот SC реализует §8.2 (паритет с CLI).
- **Uses:** [DP.SC.034] — Local Gateway: file lock семантика переиспользуется (per-user lock в agent-runner предотвращает race на одном repo).
- **Uses:** [DP.SC.036] — Routing Gate: agent-runner проверяет `agent_scopes_mvp` ДО записи.

## Auto-scope provisioning (Ф4, 2026-05-30)

Без auto-provisioning каждый новый пилот требовал ручного INSERT в `agent_scopes_mvp` — блокер для масштабирования. Решение:

- agent-runner экспонирует internal endpoint `POST /v1/admin/scope-provision` с auth `Authorization: Bearer <PROXY_SHARED_SECRET>` (НЕ Ory JWT, server-to-server).
- gateway-mcp в webhook handler `installation_repositories.added` параллельно с `triggerFullReindex` вызывает fire-and-forget `provisionAgentScopes(env, userId, owner/repo)`.
- agent-runner idempotent UPSERT: добавляет `repo_full_name` в `allowed_repos` для `run_strategist` и `run_extractor` с дефолтными `allowed_paths` из `DEFAULT_AGENT_SCOPES`. Повторный вызов — no-op (array_append с проверкой).
- Дефолтные scope: Стратег = `['docs/**', 'inbox/**', 'Strategy.md', '**/*.md']`, Экстрактор = `['inbox/**', 'docs/**']`. Пилот может сузить через manual UPDATE.

**Тест:** один webhook event → две таблицы заполнены без вмешательства пилота. Реверс при `installation_repositories.removed`: `revoked_at = now()` (через update_user_sources аналогично).

## Migration path: agent_scopes_mvp → Keto

Текущая реализация: статический map в Neon (`agent_scopes_mvp`). После деплоя Keto (WP-187 / отдельный РП) — миграция:

1. Каждая строка `agent_scopes_mvp` → permission tuple в Keto.
2. Lookup переходит на Keto `POST /relation-tuples/check`.
3. Таблица помечается `deprecated`, оставляется для аудита 90 дней, затем drop.

Имя таблицы суффикс `_mvp` фиксирует временный характер. **Условие миграции:** Keto задеплоен И прошёл нагрузочный тест 100 RPS.

## Открытые вопросы (не для MVP)

- **rate-limit per agent** (отдельно от daily cost cap пользователя): когда добавлять — после первого случая «один пользователь запускает 50 strategist подряд» или превентивно?
- **streaming-режим** для long-running агентов (`run_strategist` может работать 90 секунд) — sync timeout vs SSE. MVP: sync с увеличенным timeout (120с). Streaming — после первого пользовательского запроса.
- **batch-режим** для `run_extractor` (несколько captures за один вызов) — отложено, MVP: один capture per call.
