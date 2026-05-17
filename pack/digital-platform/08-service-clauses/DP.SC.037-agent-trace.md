---
id: DP.SC.037
title: "Журнал решений ИИ-агентов (agent trace store)"
status: draft
layer: L4-Personal
audience: [R1 Стратег, R5 Архитектор, R6 Кодировщик, DP.ROLE.047 R30 Архивариус решений]
created: 2026-05-17
updated: 2026-05-17
links: [WP-295, DP.SOTA.022, DP.SOTA.015, DP.SC.025, DP.M.060]
related:
  extends: []
  realizes: []  # Ф1 WP-295 — writer hook + projection + CLI
  uses: [DP.SOTA.022, DP.SOTA.015, DP.M.060, DP.SC.025]
---

# DP.SC.037 — Журнал решений ИИ-агентов

> **Положение:** [DP.SC.025](DP.SC.025-capture-bus.md) ловит **факты провалов** агента (P1-P11) через детекторы на harness-событиях. SC.037 покрывает другой слой — **рассуждения** агента (гипотезы, выбор, обоснования). Эти слои дополняют друг друга: SC.025 — что пошло не так, SC.037 — что агент *думал*, когда это делал.
> **Telemetry vs rationale:** [DP.SOTA.015](../06-sota/DP.SOTA.015-ai-llm-observability.md) описывает telemetry-слой (Traces / Metrics / Logs / Evaluations). SC.037 — rationale-слой (содержание рассуждений). Не пересекаются.

## Обещание

**Кому:** R1 Стратег (владелец IWE) — как потребитель trace-данных для аудита решений и эволюции правил. Pattern miner (DP.SC.040) — как читатель trace'ов для кластеризации паттернов. Replay engine (DP.SC.038) — как читатель trace'ов для восстановления состояния. Сам агент Claude — как потребитель retrieval API при OnSessionStart (похожие случаи из истории инъектируются в контекст).

**Зачем:** Главный пробел учёта IWE = ментальный слой агентов. Физические артефакты (код, события БД, РП-цикл) фиксируются; **рассуждения LLM теряются после закрытия сессии**. Без журнала решений: (1) нет outcome attribution — известно «РП закрыт», но не «какое решение к этому привело»; (2) нет replay — событие произошло, попробовать другую ветку нельзя; (3) pattern miner работает на одном слое (физические события), теряя половину сигнала. WP-295 строит этот журнал как явный сервис.

**Что получит потребитель:**

- **Trace event'ы в `learning.agent_trace.*`** — append-only поток с типами `session.start`, `decision`, `hypothesis_rejected`, `session.end`. Поле `payload`: контекст решения (входной вопрос, рассмотренные гипотезы, выбор, обоснование, ссылки на артефакты).
- **CLI `iwe trace show <session-id>`** — чтение полного trace сессии из Neon.
- **Retrieval API `iwe trace search <query>`** — гибридный retrieval (recency × similarity × importance × decision-relevance) для поиска похожих случаев. На входе — текущий контекст, на выходе — top-N похожих trace'ов с outcome.
- **OnSessionStart hook** — автоматическая инъекция top-3 похожих trace'ов в контекст агента в начале сессии (опционально, по тегу задачи).
- **Локальный лог** `~/IWE/.claude/logs/agent-trace/<session-id>.ndjson` как первичная запись (immediate, без сети, не теряется при offline).

**Критерий приёмки:**

1. Writer-hook в Claude Code пишет NDJSON в локальный лог на каждое решение агента. Async upload в event-gateway → projection в `learning.agent_trace.*`.
2. Минимум 4 типа событий (`session.start`, `decision`, `hypothesis_rejected`, `session.end`) сериализуются и читаются корректно.
3. CLI `iwe trace show <session-id>` восстанавливает полный trace в читаемом виде.
4. Retrieval API возвращает top-N результатов с весами всех 4 компонентов (recency, similarity, importance, decision-relevance) видны в ответе.
5. End-to-end smoke: одна сессия → запись в NDJSON → upload → projection → чтение через CLI без потерь.
6. Latency writer'а ≤100ms на одно событие (не блокирует hot path агента).

## Инварианты

1. **Полнота: ни одно решение не теряется.** Каждый явный выбор агента (с phrasing «решаю X», «отбрасываю Y, потому что Z») фиксируется. Локальный лог = первичный, async upload = вторичный. Потеря локального файла допустима (recovery через git, capture-bus). Потеря записи в Neon после успешного upload — алерт.
2. **Append-only events (decision, hypothesis, tool_call, snapshot, fork_session).** UPDATE/DELETE запрещены. Корректировка через compensating event (`decision_revised` со ссылкой на исходный event_id). Источник: event sourcing консенсус ([DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §1). **Исключение — `session` как card:** обновляется только на closing (`ended_at`, `closed_status`, `produced_artifact_ids`); start- и identification-поля immutable. Соответствует разделению event-log vs aggregate-card в event sourcing.
3. **Trace↔domain_event join при записи.** При записи `session.end` фиксируется список `produced_artifact_ids` — git commits, файлы, обновлённые РП. Pattern miner потом кластеризует по `(trace_features, outcome_features)` совместно, не по trace alone.
4. **Weak schema + upcasters.** JSON payload с tolerant readers. Цепочка upcasters в момент чтения переводит старые форматы в актуальный. Жёсткая схема (Protobuf strict) запрещена — горизонт эволюции trace events ≤недели.
5. **Crypto-shredding для PII.** Поля payload с потенциальной PII (email, telegram_id, имена) шифруются per-subject ключом. Удаление ключа = «забвение» под GDPR (совместимо с B7.3 PII-блокером ArchGate).
6. **Writer не блокирует.** Любая ошибка записи → exit 0, лог в capture_log, продолжение работы агента. NDJSON пишется атомарно (append-only on filesystem).
7. **Атомарный шаг = атом trace.** Принцип [DP.M.060](../03-methods/DP.M.060-atomic-vdv-step.md): один шаг = одно решение = одна запись `decision`. Не группировать несколько решений в один event.
8. **Источники недетерминизма фиксируются явно** в payload каждого `decision` event: LLM sampling params (`model`, `temperature`, `top_p`, `seed`), `timestamp` с миллисекундной точностью, `random_values` (если детерминированный seed агента не покрывает), `external_api_responses` (кешированные ответы вызванных tool'ов). Без этого SC.038 (replay) не сможет детерминированно восстановить состояние — fork даст другой результат не из-за «другой ветки», а из-за технического шума. Источник: [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §«Применимо к WP-295» п.3 (fork-replay через checkpoint + reseed недетерминизма).

## Negative guarantees (что НЕ покрывает)

- **НЕ ловит провалы поведения** (P1-P11) — это работа DP.SC.025. SC.037 пишет *рассуждение*, не *детектирует* его качество.
- **НЕ заменяет git/event-log/MEMORY.md.** Физические артефакты остаются source-of-truth. Trace — параллельный слой, ссылается на артефакты, не дублирует их содержание.
- **НЕ создаёт правила автоматически.** SC.037 — только хранение. Кандидаты-правила из паттернов — отдельный сервис (DP.SC.040 pattern-miner).
- **НЕ покрывает sessions других агентов** (Kimi, bot, headless cron) на первой итерации. Только Claude Code harness. Расширение — отдельный РП.
- **НЕ гарантирует семантическую полноту записи.** Если агент не вербализовал гипотезу («я просто решил»), запись будет пустой по hypotheses. Принцип атомарных ВДВ (DP.M.060) — предусловие, но не enforced на уровне writer'а.

## Режимы отказа

| Отказ | Симптом | Обнаружение | Восстановление |
|-------|---------|-------------|----------------|
| **NDJSON не пишется локально** | Лог сессии пуст после Edit/Write | Day Open: `ls -la .claude/logs/agent-trace/` | Проверить hook registration, permissions |
| **Async upload падает** | Локальный NDJSON растёт, в Neon ничего | Alerter cross-DB diff (NDJSON count vs `agent_trace.session` count) | Retry-loop в writer'е; backfill из NDJSON через CLI `iwe trace upload <file>` |
| **Projection-worker зависает** | Lag cursor > 1h | Существующий alerter rule 4 (cross-DB diff) | Restart projection-worker; investigate cursor |
| **Trace ↔ domain_event join пуст** | `produced_artifact_ids = []` массово | Pattern miner отчёт «trace без outcome» >20% | Доработать session-end hook (читать git status + closed WP) |
| **PII утечка в payload** | grep email/uuid в `agent_trace.*` без crypto-shred | Security audit (B7.4 monthly) | Backfill crypto-shred + явный детектор PII в writer'е |

## Сценарии использования

### SC.037.1 — Запись trace на закрытие сессии (primary)

**Триггер:** Stop hook Claude Code в конце сессии.

**Потребитель:** R1 Стратег (через CLI), pattern miner (DP.SC.040), replay engine (DP.SC.038).

**Владелец:** DP.ROLE.047 R30 Архивариус решений (writer hook + projection rules).

**Шаги:**
1. Stop hook вызывает `~/IWE/.claude/hooks/agent-trace-recorder.sh`.
2. Recorder читает буфер решений (накопленных PostToolUse-хуками за сессию) → собирает `session.end` event с `produced_artifact_ids` (git diff, новые WP, обновлённые контексты).
3. Append в локальный NDJSON `<session-id>.ndjson`.
4. Async upload в event-gateway с типом `agent.session.end`.
5. Event-gateway → projection-worker → `learning.agent_trace.session` UPSERT.

**Время отклика:** ≤100ms на запись локально. Async upload — не в hot path.

**Ожидаемый результат:** Запись в `learning.agent_trace.session` с полным trace + ссылками на артефакты.

**Симптом пропуска:** Сессия закрыта, в Neon нет записи — баг recorder'а или projection lag (см. режимы отказа).

### SC.037.2 — Retrieval похожих trace'ов в начале новой сессии

**Триггер:** OnSessionStart hook + тег задачи (WP-N, тип работы).

**Потребитель:** Сам агент Claude (контекст обогащается на старте).

**Владелец:** DP.ROLE.047 R30 Архивариус решений (retrieval API).

**Шаги:**
1. OnSessionStart hook вызывает `iwe trace search --task "<query>" --limit 3`.
2. Retrieval API считает гибридный скор по recency × similarity × importance × decision-relevance.
3. Возвращает top-3 trace'ов с outcome.
4. Hook инъектирует в контекст агента: «Похожие случаи: WP-X 12 мая (исход: ...); WP-Y 7 мая (исход: ...)».

**Время:** ≤500ms на retrieval (не блокирует старт сессии, можно lazy).

**Ожидаемый результат:** Агент стартует с памятью о похожих случаях. На pre-implementation gate использует их для решения.

**Симптом пропуска:** Агент повторяет ошибку из trace месячной давности — retrieval не сработал или score-формула некорректна.

### SC.037.3 — Чтение trace для audit / debug

**Триггер:** R1 Стратег расследует, как агент пришёл к решению.

**Потребитель:** R1 Стратег (человек).

**Владелец:** Сам R1 через CLI.

**Шаги:**
1. `iwe trace show <session-id>` (или поиск через `iwe trace search`).
2. CLI выводит хронологию: session.start → decisions (с гипотезами и обоснованиями) → session.end.
3. Каждый decision показывает: рассмотренные гипотезы, выбор, обоснование, артефакт-результат.

**Время:** интерактивно (≤2 сек на чтение из Neon).

**Ожидаемый результат:** R1 видит полную картину решений сессии, может проследить причинно-следственные связи.

**Симптом пропуска:** CLI показывает пустой trace для активной сессии — writer не записал (см. SC.037.1 симптомы).

## Реализующие сервисы (план Ф1 WP-295)

| Сервис | Роль | Триггер | Путь |
|--------|------|---------|------|
| S-TBD-agent-trace-recorder | TBD R30 Архивариус | PostToolUse + Stop | `~/IWE/.claude/hooks/agent-trace-recorder.sh` |
| S-TBD-trace-projection | TBD R30 + WP-270 pattern | `agent.trace.*` events | `multi-domain-projection-worker` (handler) |
| S-TBD-trace-cli | TBD R30 | manual | `iwe trace {show,search,upload}` |

## Связь с другими обещаниями

- **Uses:** [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) (rationale-слой), [DP.SOTA.015](../06-sota/DP.SOTA.015-ai-llm-observability.md) (telemetry-слой), [DP.M.060](../03-methods/DP.M.060-atomic-vdv-step.md) (атомарность как предусловие), [DP.SC.025](DP.SC.025-capture-bus.md) (детекторы провалов, ортогональны).
- **Feeds:** DP.SC.038 (replay engine читает trace), DP.SC.040 (pattern miner читает trace + outcome join).
- **Related:** WP-217 (capture-bus), WP-253 (event sourcing), WP-270 (projection-rules), WP-244 (Platform Observability).
- **Potentially-conflicts:** нет. SC.037 — write-side; consumers независимы.

---

**Статус:** draft, 17 мая 2026. Реализация: Ф1 WP-295 (~12-14h). Требует: (1) ArchGate Ф0.5 (закрыть 4 открытых вопроса DP.SOTA.022), (2) регистрации DP.ROLE.047 R30 Архивариус решений в DP.ROLE.001 (Ф0.4), (3) миграции БД (схема `agent_trace` в `learning`).
