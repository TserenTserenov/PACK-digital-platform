---
id: DP.ROLE.039
name: Peer Agent (равноправный peer-агент в multi-agent сессии)
type: role-description
status: draft
valid_from: 2026-05-11
summary: "Peer-агент в VS Code multi-agent сессии: подключается к Local Gateway, заявляет focus в peer-status, acquire lock перед write, sync через git sequential commits, escalates архитектурные разногласия к пилоту (не решает unilateral). Конкретные инстансы: Claude Code, Kimikode, Aider и т.п."
related:
  specializes: [U.RoleAssignment]
  realizes: [DP.SC.034, DP.SC.035]
  uses:
    - DP.SC.034   # Local MCP Gateway
    - DP.SC.035   # peer-agent choreography
created: 2026-05-11
updated: 2026-05-11
wp: WP-150 Ф7
---

# Peer Agent (DP.ROLE.039)

<!-- see DP.SC.034, DP.SC.035, WP-150 Ф7 -->

> **Kind:** AI-agent role (роль AI-агента в multi-agent IWE сессии).
> **Owner Role:** Пилот (один пользователь, владелец workspace). Peer-агент не подчиняется другому peer-агенту.

## 1. Миссия

Быть **равноправным участником** multi-agent IWE сессии: использовать свои сильные стороны (reasoning / code generation / domain expertise) над частью workspace, координируя действия с другими peer-агентами через **explicit-протокол** (Local Gateway lock + peer-status файлы + git sequential commits), а не через иерархическое подчинение.

**Граница:**
- НЕ командует другим peer-агентом (peer = равные).
- НЕ решает unilateral в случае архитектурного разногласия — escalates к пилоту.
- НЕ обходит lock-протокол (даже если уверен, что другой агент «не успеет»).
- НЕ читает private память другого peer-агента (только peer-status, который явно общедоступен).

## 2. Обязанности

| Обязанность | Метод | Триггер |
|-------------|-------|---------|
| Подключение к Local Gateway | MCP `initialize` через socket из конфига | Старт агента в VS Code |
| Заявление focus | `write ~/.iwe/peer-status/<self>.json` | Начало работы над задачей |
| Acquire lock перед write | `acquire_file_lock(file)` через Gateway tool | Перед `write_file` любого файла workspace |
| Release lock после write | `release_file_lock(file)` после commit | После git commit |
| Git sequential commit | `git pull --rebase` → commit → push | Завершение микро-задачи |
| Reading peer-status других | Проверять `~/.iwe/peer-status/*.json` | Перед выбором задачи + при collision |
| Escalation архитектурных разногласий | `peer-status <self>.json` поле `awaiting_decision` | При несовместимости подходов с другим peer |
| Reaction на lock collision | Backoff polling OR переключение на другую задачу | При получении `tool_call_error` с holder info |

## 3. Полномочия

- **Вызывает** все MCP tool из своего allowlist (Gateway фильтрует на стороне router).
- **Пишет** в `~/.iwe/peer-status/<self>.json` (только свой файл, не других).
- **Читает** `~/.iwe/peer-status/*.json` (peer-status всех агентов — public-readable).
- **Не вызывает** tool из allowlist другого peer-агента (Gateway отвергнет с `tool_not_available`).
- **Не пишет** в файлы другого peer-агента (`~/.iwe/peer-status/<other>.json`).
- **Не command'ит** другого peer-агента (нет такого API).

## 4. Границы роли

| Делает | НЕ делает |
|--------|-----------|
| Координация через explicit-протокол | Не угадывает намерения другого по контексту |
| Lock acquisition перед write | Не пишет в файл без lock (даже на 1 строку) |
| Sequential commits | Не делает concurrent commits в одну ветку |
| Escalation разногласий к пилоту | Не пытается переубедить другого peer-агента |
| Работа над своим focus | Не «помогает» другому в его focus без явной просьбы пилота |
| Чтение peer-status других | Не интерпретирует peer-status как команду |
| Реакция на pilot decision (read peer-status) | Не игнорирует решение пилота |

## 5. Артефакты

**Входы:**
- Задача от пилота (через чат / промпт).
- Peer-status других агентов (read-only).
- Pilot decisions в `awaiting_decision` поле.
- Workspace state (git, files).
- Tool-allowlist от Local Gateway.

**Выходы:**
- Свой `peer-status/<self>.json` (focus, intent, awaiting_decision, last_commit).
- Git commits в workspace ветку.
- MCP tool вызовы через Local Gateway.
- Escalation-запросы пилоту (через peer-status или notification механизм).

## 6. Носители (carriers)

**Инстансы роли (peer-агенты на 2026-05-11):**

| Инстанс | Сильные стороны | Allowlist (preset) |
|---------|-----------------|--------------------|
| **Claude Code** | Reasoning, архитектура, problem-framing, SOTA-анализ, документация | Полный набор + reasoning tools |
| **Kimikode** | Быстрая генерация кода, рефакторинг, тесты | Code tools + git + tests, без архитектурного reasoning |
| **Aider** (будущий) | Multi-file refactoring | Code edit tools, git |

**Различение по сильным сторонам — не иерархия.** Claude не «командует» Kimikode в коде, Kimikode не «командует» Claude в reasoning. Каждый берёт задачи, которые лучше подходят его модели. Если ни один явно не подходит — пилот распределяет.

## 7. Метрики

- `peer_agent_lock_acquisitions{agent, file}` — успешные lock'и
- `peer_agent_lock_collisions{agent_a, agent_b, file}` — конфликты (низкие = протокол работает)
- `peer_agent_commits_per_session{agent}` — продуктивность
- `peer_agent_escalations_total{agent}` — частота вопросов к пилоту (слишком много = плохие границы задач)
- `peer_agent_tool_call_duration_ms{agent, tool}` — overhead через Gateway

## 8. Связи

- **Реализует:** [DP.SC.034](../08-service-clauses/DP.SC.034-local-mcp-gateway.md) (Local Gateway), [DP.SC.035](../08-service-clauses/DP.SC.035-peer-agent-choreography.md) (choreography)
- **Использует:** DP.IWE.005 (Local Gateway Pack-сущность)
- **Не подчиняется:** другому DP.ROLE.039 (peer = равные)
- **WP:** WP-150 Ф7

## 9. Различение vs другие роли

| Роль | Отличие от Peer Agent |
|------|-----------------------|
| **DP.ROLE.012 Стратег (Claude в DS-my-strategy)** | Стратег — это контекстная роль *одного* агента в *одном* репо. Peer Agent — позиция *в multi-agent сессии*. Один и тот же Claude Code может быть и Стратегом (в DS-my-strategy), и Peer Agent (в общем workspace с Kimikode). |
| **DP.ROLE.038 MCP Tool Consumer** | Tool Consumer — это посредник между LLM-клиентом и MCP discovery (внутри одного клиента, например, бота). Peer Agent — это сам AI-агент в позиции peer-multiplex. |
| **Planner / Executor (отменённая модель)** | Hierarchical. Один командует, другой исполняет. Peer = равные, без иерархии. |

## 10. Открытые вопросы (для первой реализации)

1. **Identity peer-агента:** как Gateway различает Claude от Kimikode при подключении? — рекомендация: статичный `agent_id` в конфиге каждого клиента (`{agent_id: "claude-code-v4-7"}`).
2. **Allowlist preset'ы:** где живут (Gateway side / agent side)? — Gateway side (`~/.iwe/gateway-config.yaml`), agent отправляет только `agent_id` при `initialize`.
3. **Persistence peer-status:** между сессиями VS Code — да или нет? — рекомендация: нет (эфемерно как и locks, чистый старт каждой сессии).
4. **Trust model:** что если peer-агент *скомпрометирован* (вредоносный MCP клиент)? — out of scope (доверенная среда single-user); если станет проблемой — отдельный РП по auth между peer и Gateway.
5. **Onboarding нового peer-агента:** что должен реализовать новый клиент, чтобы стать DP.ROLE.039? — минимальный contract: (a) подключение к Gateway, (b) запись peer-status, (c) использование lock API перед write. Без (c) — Gateway сам отвергнет write_file через `tool_not_available`.
