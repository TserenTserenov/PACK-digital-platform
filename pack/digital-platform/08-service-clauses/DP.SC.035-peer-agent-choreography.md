---
id: DP.SC.035
name: Peer-agent choreography (turn-based координация)
name_ru: Хореография peer-агентов (turn-based координация)
name_en: Peer-agent choreography (turn-based coordination)
type: sc
status: draft
layer: L4-Personal
summary: "Пилот получает гарантию: два+ peer-агента (Claude Code + Kimikode и др.) в одной VS Code сессии работают параллельно над разными файлами без дублирования и race-condition. Координация — turn-based через lock API Local Gateway + git sequential commits для sync."
consumer: "Пилот (один пользователь, ≥2 peer-агента в сессии)"
created: 2026-05-11
updated: 2026-05-11
related:
  realizes: [DP.IWE.005]
  extends: [DP.SC.034]
  uses: [DP.SC.034]
  role: DP.ROLE.039
wp: WP-150 Ф7
---

# [DP.SC.035] Peer-agent choreography (turn-based координация)

> **Различение:** это **не Planner→Executor** (hierarchical). Peer-агенты равноправны, ни один не «командует» другим. Координация — через explicit-протокол lock-acquisition + turn-taking, не через подчинение. См. отменённый раздел Ф7 в `DS-my-strategy/inbox/WP-150-multi-agent-architecture.md` (superseded 2026-05-11 после Ф0 decision doc).

## Правило (инвариант)

> Что ВСЕГДА должно выполняться. Нарушение = провал SC.

- Никакой peer-агент не имеет «командных» полномочий над другим (нет ролей Planner / Executor / Resolver).
- Перед write в файл — обязательный `acquire_file_lock` через Local Gateway (DP.SC.034).
- Если lock collision — агент **не** ломится, не переписывает, не игнорирует: либо ждёт (polling backoff), либо переключается на другую задачу из своего todo.
- Синхронизация результатов между peer-агентами — через **git sequential commits** в общую ветку (один за раз, не concurrent).
- Каждый peer-агент пишет в `~/.iwe/peer-status/<agent>.json` свой текущий focus (file + intent) — для видимости другим (advisory, не enforcement).
- Конфликт мнений о подходе → **не arbiter, а пользователь** (peer-агенты ставят вопрос пилоту, ждут решения).

## Обещание

**Кому:** Пилот, открывший VS Code сессию с ≥2 peer-агентами (Claude Code + Kimikode и др.).

**Зачем:** Без turn-based протокола два агента в одном workspace либо дублируют работу (оба чинят один баг), либо повреждают результат (оба пишут в `src/foo.py`, чьи изменения «победили» — рандом). Hierarchical Planner→Executor мог бы решить, но искусственно понижает компетентного агента. Peer-протокол сохраняет полную компетенцию обоих.

**Что получит:**
- Гарантию, что в каждый момент **один файл редактируется одним агентом** (через DP.SC.034 lock).
- Видимость состояния: «кто над чем сейчас работает» (peer-status файлы + `gateway_status` tool).
- Sequential git commits: история линейная, нет merge conflicts от concurrent edits.
- При архитектурном разногласии — escalation к пилоту (peer-агент пишет в `~/.iwe/peer-status/<agent>.json` поле `awaiting_decision: "..."`).

**Триггер:**
- Старт сессии с ≥2 peer-агентами.
- Намерение peer-агента write в файл → проверка peer-status + lock acquisition.
- Завершение микро-задачи peer-агента → git commit + обновление peer-status.

**Время отклика:**
- Lock check ≤50ms (через Gateway).
- Git commit ≤2s (small commit).
- Decision escalation: асинхронно (peer-агент продолжает другую задачу, ждёт пилота).

**Режим отказа:**
- Lock TTL истёк (зависший агент) → автоматический release + warning в peer-status «agent X timed out on file Y».
- Git commit fail (conflict с предыдущим commit) → peer-агент делает `git pull --rebase`, повторяет.
- Peer-status файл повреждён / отсутствует → fallback: агент работает «слепо» (только через lock API), без видимости намерений других.
- Архитектурное разногласие → блок до решения пилота (peer-агент **не** делает unilateral choice).

## Свидетельства (критерий приёмки)

**Данные** (что фактически существует):

| Критерий | Как проверить |
|----------|--------------|
| Peer-status файлы существуют | `ls ~/.iwe/peer-status/*.json` — по одному на агента |
| Lock-протокол соблюдается | grep tool-вызовов в Gateway log: `write_file` следует за `acquire_file_lock` того же агента |
| Git история линейна | `git log --graph` показывает single line, no merges из peer-веток |
| Нет concurrent edits | `gateway_lock_collision_total` остаётся низким; высокий счётчик = протокол нарушается |
| Escalation поднимается к пилоту | `~/.iwe/peer-status/*.json` поле `awaiting_decision` → notification пилоту |

**Контекст** (при каких условиях обещание действует):

| Условие | Проверка |
|---------|---------|
| Local Gateway запущен (DP.SC.034 активен) | `gateway_status` отвечает |
| Все peer-агенты соблюдают протокол (используют lock API, не пишут в обход) | Code review агентов или MCP-обёртка `write_file` форсирует lock |
| Git репо чист на старте сессии | `git status` clean |

**Полномочия** (кто уполномочен подтвердить):

| Роль | Что подтверждает |
|------|-----------------|
| Пилот | Корректность escalation-решений |
| DP.ROLE.039 Peer Agent | Соблюдение протокола со своей стороны |
| Платформенная команда | Архитектурную целостность (no Planner-mode скрытно) |

**Свидетельства** (как узнать, что обещание выполнено):

| Свидетельство | Источник |
|--------------|---------|
| Sequential git commits в workspace ветке | `git log --oneline --since="session start"` |
| Низкий `gateway_lock_collision_total` | Gateway metrics |
| Peer-status файлы обновляются в realtime | `find ~/.iwe/peer-status -mmin -5` |

## Реализующие сервисы

| Сервис | Роль | Триггер |
|--------|------|---------|
| Local Gateway lock API (DP.SC.034) | Транспорт для координации | 🤖 каждый write peer-агента |
| `peer-status` файлы (`~/.iwe/peer-status/<agent>.json`) | Видимость намерений | 🤖 обновляется агентом при смене focus |
| Git pre-write hook (опционально) | Enforcement lock-протокола | 🤖 проверяет lock перед commit |
| Notification пилоту при escalation | Сигнал о разногласии | 🤖 при `awaiting_decision` ≠ null |

## Пользовательский путь (happy path)

| # | Шаг | Кто | Инструмент |
|---|-----|-----|-----------|
| 1 | Пилот открывает VS Code с двумя peer-агентами | Пилот | VS Code + Local Gateway (DP.SC.034) |
| 2 | Claude обновляет `peer-status/claude.json`: `{focus: "src/auth.py", intent: "refactor"}` | Claude | write file |
| 3 | Kimikode читает peer-status, видит занятость auth.py → выбирает `tests/test_auth.py` | Kimikode | read file |
| 4 | Kimikode обновляет `peer-status/kimikode.json`: `{focus: "tests/test_auth.py"}` | Kimikode | write file |
| 5 | Оба acquire lock, оба пишут параллельно (разные файлы) | оба | DP.SC.034 |
| 6 | Claude закончил → commit `refactor auth` → release lock → peer-status `focus: null` | Claude | git + lock release |
| 7 | Kimikode видит освобождённый auth.py, заметил архитектурное разногласие → escalation | Kimikode | write peer-status `awaiting_decision: "..."` |
| 8 | Пилот видит notification, принимает решение | Пилот | — |
| 9 | Kimikode читает решение, продолжает работу | Kimikode | — |

## Сценарии использования (минимум 3)

### Сценарий 1: Parallel feature implementation

Claude проектирует и реализует backend endpoint в `api/users.py`. Kimikode параллельно пишет тесты в `tests/test_users.py`. Каждый держит lock на свой файл. Sync через два sequential git commits (Claude первым → push → Kimikode pull rebase → commit). Никакого Planner-режима не нужно — оба знают свою задачу и согласовывают только interface через peer-status.

### Сценарий 2: Lock-collision → graceful turn-taking

Оба независимо решили коснуться `src/index.ts` (Claude хочет обновить routing, Kimikode — добавить новый import). Claude получает lock первым. Kimikode видит collision → читает peer-status Claude → понимает «refactor routing» → выбирает: либо ждать (polling), либо переключиться на другую задачу из своего todo. Через 3 минуты Claude release → Kimikode acquire → делает свою правку. Sequential commits в git.

### Сценарий 3: Architectural disagreement → escalation к пилоту

Claude предлагает использовать Redux для state management, Kimikode уже начал делать через Zustand. Оба видят несовместимость подходов через peer-status (focus + intent). **Не пытаются переубедить друг друга** (peer = равные). Один из них (тот, кто заметил первым) пишет в peer-status `awaiting_decision: "Redux vs Zustand for state — see comment in src/store/README.md"` и переключается на независимую задачу. Пилот получает notification, читает обсуждение, принимает решение. Тот, кто проиграл — переделывает свою работу через стандартный lock-протокол.

## Связь с другими обещаниями

- Реализует: **DP.IWE.005** (Local Gateway Pack-сущность)
- Использует: **DP.SC.034** (Local MCP Gateway — транспорт для lock API)
- Парный SC: **DP.SC.034** (Gateway = «как», choreography = «что делать»)
- Не пересекается с: **Planner→Executor** (отменённая модель из старого Ф7)
- Носитель роли: **DP.ROLE.039** Peer Agent

## Открытые вопросы (для реализации, не блокируют спецификацию)

1. **Enforcement vs advisory:** должен ли git pre-commit hook отвергать commit без lock? — рекомендация: advisory на старте, enforcement после первого инцидента lock-bypass.
2. **Peer-status schema:** какие поля обязательны (`focus`, `intent`, `awaiting_decision`, `started_at`)? — спецификация в первой реализации.
3. **Notification механизм при escalation:** TG бот / VS Code notification / просто файл-watch? — отдельно после MVP.
4. **Conflict resolution rules:** что если оба одновременно подняли `awaiting_decision` по противоречивым вопросам? — пилот решает последовательно (один-за-другим).
5. **Tracking «who decided what»:** нужен ли журнал escalation-решений? — рекомендация: писать в `~/.iwe/peer-status/decisions.log` (append-only).
