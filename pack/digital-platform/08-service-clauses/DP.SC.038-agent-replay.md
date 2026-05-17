---
id: DP.SC.038
title: "Повтор сессий ИИ-агента с форком (agent replay)"
status: draft
layer: L4-Personal
audience: [R1 Стратег, R5 Архитектор, TBD R31 Машина повторов]
created: 2026-05-17
updated: 2026-05-17
links: [WP-295, DP.SOTA.022, DP.SC.037, WP-253]
related:
  extends: []
  realizes: []  # Ф2 WP-295
  uses: [DP.SC.037, DP.SOTA.022]
---

# DP.SC.038 — Повтор сессий ИИ-агента с форком

> **Положение в WP-295:** SC.037 пишет trace, SC.038 его *читает* для восстановления состояния и форка альтернативной ветки. Без SC.037 невозможен.

## Обещание

**Кому:** R1 Стратег — как заказчик «попробовать другой выбор» на архитектурных решениях. R5 Архитектор — как пользователь replay для воспроизведения багов и учебных кейсов. Сам агент Claude — как исполнитель форка (получает восстановленный контекст и продолжает).

**Зачем:** В IWE решение зафиксировано в git, но **гипотезы и альтернативы — нет** (это SC.037). Когда задним числом становится понятно, что выбор был неверный, пересобрать состояние на момент решения и пройти другой веткой — единственный способ научиться на конкретном кейсе. Без replay: либо переоткрывать вручную (дорого, ненадёжно — теряется недетерминизм), либо забывать (потеря урока). В industry такой механизм называют durable execution (Temporal) или time-travel debugging (LangGraph) — рабочая практика на 2024-2026.

**Что получит потребитель:**

- **CLI `iwe replay <event-id> --fork [--branch <name>] [--message "..."]`** — восстанавливает контекст на момент event_id, создаёт fork-сессию.
- **Восстановленный контекст** — собирается из `learning.domain_event` + `learning.agent_trace.*` до момента event_id: открытые гипотезы, артефакты в работе, прочитанные файлы. Сериализуется в формат, подходящий для инъекции в Claude Code (начальный prompt + список «как-бы-прочитанных» файлов).
- **Запись fork-метаданных** в `agent_trace.fork_session`: `parent_event_id`, `fork_branch_name`, `forked_at`, `selector_decision` (если форк сделан как часть multi-path).
- **Сохранение всех источников недетерминизма** — timestamps, LLM-sampling выборы (temperature/top-p/seed), random values, ответы внешних API. Без этого fork даёт другой результат не из-за «другой ветки», а из-за технического шума.
- **Parent-child trace link** — новая сессия в `agent_trace.session` имеет поле `parent_event_id` → можно построить дерево форков.

**Критерий приёмки:**

1. CLI `iwe replay <event-id> --fork` запускается на любом event из последних 30 дней (retention TBD) и возвращает контекст без потерь.
2. Восстановленный контекст содержит: входной вопрос пользователя на момент event_id, открытые гипотезы, ссылки на артефакты в работе, источники недетерминизма (seed, sampling params).
3. Fork-сессия записывает `parent_event_id` в `agent_trace.session` — дерево форков визуализируется через CLI `iwe trace tree <root-session-id>`.
4. Smoke: replay недавнего event WP-295 → восстановление контекста → форк → новый trace с parent-link.
5. Detерминированность: replay одного и того же event_id дважды без изменения параметров даёт идентичные восстановленные контексты (byte-equal).

## Инварианты

1. **Детерминированное восстановление до момента T.** Replay читает `domain_event` + `agent_trace.*` где `created_at <= T`. Никаких событий после T не видно. Это обеспечивает воспроизводимость.
2. **Все источники недетерминизма зафиксированы при записи** (требование к SC.037). При forge без явного override используются ТЕ ЖЕ значения. Override — explicit (`iwe replay <id> --new-seed 42 --new-temperature 0.3`).
3. **Read-only по исходному trace.** Replay никогда не модифицирует `domain_event` или `agent_trace.session` родительской сессии. Все новые записи — в новую `fork_session`.
4. **Parent-child граф append-only.** Удаление родительской сессии запрещено до удаления всех её потомков (FK или application-level guard). Иначе дерево распадается.
5. **Retention-aware.** Если события за период удалены retention policy — explicit error «events removed by retention policy», не silent partial replay. Источник: SOTA-граница [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §«Что НЕ брать».
6. **Snapshot-aware при длинных сессиях.** Восстановление не O(длина истории) — используется ближайший snapshot ≤ T + хвост событий между snapshot и T. Snapshot policy: каждые 20-50 решений или 5-10 минут (отправная точка, калибровать).

## Negative guarantees

- **НЕ воспроизводит внешний мир.** Если решение зависело от ответа внешнего API, который с тех пор изменился — replay использует кешированный ответ (если был сохранён в trace) или возвращает explicit warning. Не делает живой запрос к API «как тогда».
- **НЕ восстанавливает state файловой системы.** Если файл был изменён/удалён после event_id — replay читает git history до соответствующего commit'а, не текущее состояние диска.
- **НЕ применяет результат форка автоматически.** Fork-сессия завершается так же, как обычная сессия — commit, push явные. Слияние результатов в основную ветку — отдельное решение R1.
- **НЕ покрывает sessions других агентов на первой итерации** (Kimi, bot, headless cron). Только Claude Code, источник trace = SC.037.
- **НЕ replay'ит цепочки длиннее retention.** Текущее значение TBD (стартовое: hot 30 дней / warm 6 мес / cold 2 года через S3). После — explicit error.

## Режимы отказа

| Отказ | Симптом | Обнаружение | Восстановление |
|-------|---------|-------------|----------------|
| **Event_id не найден** | CLI error «event not found» | Smoke-test после миграции | Проверить retention; cold storage retrieval |
| **Недетерминизм не зафиксирован** | Replay даёт другой результат | Diff trace original vs replay | Backfill: добавить missing nondeterminism sources в SC.037 writer |
| **Snapshot отсутствует** | Replay O(длина истории), >30s на длинную сессию | Latency monitor `iwe replay` | Дозаписать snapshot postfactum или сделать запись lazy |
| **Parent-child link сломан** | `iwe trace tree` обрывается | Periodic audit cron | Application-level repair; explicit warning |
| **Cached external response устарел** | Replay даёт устаревший контекст | Diff produced_artifacts original vs fork | Явный flag `--allow-stale-cache` или live re-fetch с warning |

## Сценарии использования

### SC.038.1 — Архитектурный «что-если» (primary)

**Триггер:** R1 видит в trace, что 2 недели назад на ArchGate выбран подход A. Хочет проверить, что было бы с подходом B.

**Потребитель:** R1 Стратег.

**Владелец:** TBD R31 Машина повторов (CLI + восстановление).

**Шаги:**
1. R1 находит event_id ArchGate-решения через `iwe trace search "ArchGate WP-N"`.
2. `iwe replay <event-id> --fork --branch "wp-N-alt-B" --message "Try option B instead"`.
3. CLI восстанавливает контекст на момент ArchGate: открытые опции, критерии ЭМОГССБ, рассмотренные альтернативы.
4. Создаёт новую сессию с `parent_event_id`. Агент стартует с восстановленным контекстом.
5. Агент проходит ArchGate заново — выбирает B вместо A.
6. Fork-сессия закрывается, в `agent_trace.fork_session` запись с `selector_decision: human_override`.

**Время:** ≤2 сек на восстановление + время сессии (как обычно).

**Ожидаемый результат:** Две trace-ветки в `iwe trace tree <ArchGate-event>` — оригинал и форк. R1 сравнивает исходы.

**Симптом пропуска:** Fork дал результат, идентичный оригиналу (без override) → недетерминизм не зафиксирован, ищем источник.

### SC.038.2 — Воспроизведение баг-репорта

**Триггер:** Пользователь сообщает «вчера агент странно повёл себя в сессии X».

**Потребитель:** R5 Архитектор (debug).

**Владелец:** TBD R31 Машина повторов.

**Шаги:**
1. `iwe trace search "user-complaint-keyword"` → находит сессию.
2. `iwe replay <session-start-event> --fork --branch "bug-repro-YYYY-MM-DD"`.
3. Восстановление контекста + replay шагов.
4. Анализ: какой именно decision привёл к проблеме?
5. Patch в правилах / hook'ах / промптах.

**Время:** интерактивно.

**Ожидаемый результат:** Воспроизведённое странное поведение → понимание корня → fix.

### SC.038.3 — Учебный случай: «как было сделано» + «попробуй сам»

**Триггер:** Onboarding нового агента (Kimi, peer, headless) на класс задач.

**Потребитель:** R5 Архитектор (готовит учебный материал), новый агент (исполнитель).

**Владелец:** TBD R31 + R5.

**Шаги:**
1. R5 выбирает 3-5 эталонных trace'ов из истории (`iwe trace search --tag exemplary`).
2. Для каждого: `iwe replay <event-id> --fork --branch "training-<new-agent>" --read-only-context`.
3. Новый агент получает: восстановленный контекст + комментарий «как было сделано раньше — попробуй свой путь».
4. Сравнение результатов: новый агент vs эталон по ЭМОГССБ.

**Время:** 30-60 мин на 5 кейсов.

**Ожидаемый результат:** Новый агент калиброван, R5 видит расхождения и решает — учить или принимать как валидную альтернативу.

## Реализующие сервисы (план Ф2 WP-295)

| Сервис | Роль | Триггер | Путь |
|--------|------|---------|------|
| S-TBD-replay-engine | TBD R31 Машина повторов | CLI | `iwe replay`, библиотека restore_context() |
| S-TBD-snapshot-writer | TBD R30 Архивариус + R31 | каждые 20-50 решений | `~/IWE/.claude/lib/snapshot_writer.sh` |
| S-TBD-trace-tree-cli | TBD R31 | CLI | `iwe trace tree <session-id>` |

## Связь с другими обещаниями

- **Uses:** [DP.SC.037](DP.SC.037-agent-trace.md) (без trace нет replay), [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §1 (event sourcing паттерны).
- **Feeds:** Multi-path scheduler (DP.SC.039) — каждый параллельный путь = fork от одной начальной точки; pattern miner (DP.SC.040) — replay для проверки гипотез о причинах провалов.
- **Related:** WP-253 (event sourcing infra), Temporal/EventStoreDB как референсные модели.
- **Potentially-conflicts:** WP-253 если решит свой replay-механизм (дублирование). Решение на ArchGate Ф0.5: replay живёт в WP-295, WP-253 — только event sourcing.

---

**Статус:** draft, 17 мая 2026. Реализация: Ф2 WP-295 (~12h). Требует: SC.037 в проде, snapshot policy финализирована на ArchGate Ф0.5.
