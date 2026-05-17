---
id: DP.SC.040
title: "Кандидаты правил из паттернов trace (pattern miner)"
status: draft
layer: L4-Personal
audience: [R1 Стратег, R5 Архитектор, R15 Применитель захватов, DP.ROLE.050 R33 Старатель паттернов]
created: 2026-05-17
updated: 2026-05-17
links: [WP-295, DP.SOTA.022, DP.SC.037, WP-272, DP.SC.024]
related:
  extends: []
  realizes: []  # Ф4 WP-295
  uses: [DP.SC.037, DP.SOTA.022, WP-272]
---

# DP.SC.040 — Кандидаты правил из паттернов trace

> **Положение:** SC.037 пишет trace, SC.040 их *кластеризует* и предлагает кандидатов на новые правила [`AR.NNN.md`](https://github.com/PACK-agent-rules). Финальное утверждение — за человеком (R1 + R15 на apply-captures). Полная автоматизация генерации правил **не работает** (SOTA failure attribution 2025: <10% accuracy) — поэтому SC.040 = гибридный workflow, не auto-rule generation.

## Обещание

**Кому:** R1 Стратег (владелец эволюции правил IWE) — главный потребитель кандидатов. R15 Применитель захватов — применяет принятые кандидаты в Pack/AR. R5 Архитектор — на ревью кандидатов для системных решений.

**Зачем:** Без pattern miner: 50+ trace'ов за месяц лежат как сырой материал; правила обновляются по горячим следам отдельных эпизодов («вот это сломалось, добавим правило»). Pattern miner агрегирует — даёт топ-3 паттерна провалов / успехов за период; человек решает, превращать ли в правило. Это закрывает цикл «событие → урок → правило → меньше событий». Без этого цикла учёт превращается в архив, не в обучение.

Гипотеза индустрии (LangSmith Insights, Phoenix anomaly detection): кластеризация хороша для приоритизации; finale-utверждение — человек.

**Что получит потребитель:**

- **Отчёт «топ-3 паттерна» в Week Close** — markdown в `DS-my-strategy/current/inbox/pattern-miner-W{N}.md`. Каждый паттерн: тип (provider/успех), частота, severity, кластерные примеры (5-10 trace_id со ссылками), предлагаемый кандидат-правило (черновик AR.NNN с frontmatter).
- **Триггерный отчёт** при N≥3 одинаковых ошибках в trace за 7 дней — отдельный TG-алерт + срочный отчёт в inbox (не ждать Week Close).
- **Status «pending-review» для кандидатов** — в фронтматтере отчёта `status: pending-review`. R15 на apply-captures решает accept/reject/defer.
- **Cross-correlation с domain_event** — кандидат содержит `(trace_features, outcome_features)` join: какой downstream-артефакт был у trace'ов (commit hash, файлы, исход РП). Это причинность, не корреляция по embedding.
- **CLI `iwe miner candidates --since 2026-05-01`** — на любой период.
- **Audit-log** того, какие кандидаты были accepted/rejected/deferred — для калибровки самого майнера (P/R по времени).

**Критерий приёмки:**

1. Pattern miner запускается на еженедельной основе (Week Close R-вопросник или cron); генерирует ≥1 кандидата за неделю работы агента (если 0 — alert «trace data not flowing»).
2. Каждый кандидат содержит: примеры trace_id, частоту, severity, frontmatter draft AR.NNN, cross-correlation с produced_artifacts.
3. Триггерный алерт на N≥3 одинаковых ошибках срабатывает в течение ≤24h после третьего инцидента.
4. R15 на apply-captures обрабатывает кандидатов через стандартный extraction-report flow ([DP.SC.024](DP.SC.024-iwe-maintenance.md) тоже на R-вопроснике). **Формат:** `pattern-miner-W{N}.md` пишется как extraction-report-совместимый документ — обязательный frontmatter `status: pending-review` + `capture_source: pattern_miner` + `capture_method: trace_clustering` + список `candidates` (массив draft AR.NNN с собственными frontmatter). R15 читает этот файл через тот же flow, что и обычные extraction-reports (apply-captures skill), отдельный workflow не требуется.
5. Audit-log accept/reject/defer ведётся с обоснованием — материал для калибровки.

## Инварианты

1. **Никогда не создаёт правила автоматически.** SC.040 предлагает; человек утверждает (R15 на apply-captures). Источник: SOTA failure attribution 2025 (AgenTracer и follow-up: автоматическая accuracy <10%). Этот инвариант — главное отличие от reward-modelling систем.
2. **Кластеризация по `(trace_features, outcome_features)`, не только trace alone.** Embedding similarity по тексту trace ловит «похожее по форме»; реальная причинность — в outcome. Источник: [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §«Что брать» п.4.
3. **Provenance каждого кандидата.** AR.NNN frontmatter содержит `derived_from: [trace_id_1, trace_id_2, ...]`. Для аудита, replay, отката. Совместимо с OwnerIntegrity: один факт — одно место (трасса — оригинал, правило — производная).
4. **Read-only по trace.** Майнер не модифицирует `agent_trace.*`. Все эффекты — отчёты в `pattern-miner-W{N}.md` + audit-log.
5. **Гибридный retrieval для похожих кандидатов.** При формировании кластера: recency × similarity × importance × decision-relevance. Не голый cosine. Источник: LongMemEvalS (ICLR 2025): retrieval-stage оптимизации +9.4 п.п.
6. **Status: pending-review для всех кандидатов.** Никакой кандидат не считается «выводом», пока R15 не сделал accept. Это вход в систему apply-captures, не выход в Pack.
7. **Audit-loop калибровки.** Каждые 4 недели — отчёт «P/R майнера»: сколько кандидатов было accepted, сколько rejected, сколько deferred. Если reject-rate > 60% — пересмотр алгоритма кластеризации (false-positive выше humano-acceptable).

## Negative guarantees

- **НЕ заменяет capture-bus (DP.SC.025).** SC.025 ловит провалы в момент совершения (real-time детекторы). SC.040 — batch retrospective. Разные слои.
- **НЕ заменяет R-вопросник Week Close.** SC.040 даёт сырьё (кандидаты); R-вопросник — место решения (что делать с кандидатами).
- **НЕ кластеризует «похожее по форме».** Эмбеддинги — только один из 4 компонентов скора. Чистое embedding-кластеризование выдаёт ложные паттерны (см. SOTA анти-паттерны).
- **НЕ работает без physical outcome join.** Если SC.037 не записал `produced_artifact_ids` (severe drift), майнер выдаёт warning «trace data incomplete, candidates may be spurious».
- **НЕ ловит провалы новых классов автоматически.** Открытый каталог: paтtern_type определяется кластеризацией, но семантика паттерна («что это значит») — за человеком на ревью.

## Режимы отказа

| Отказ | Симптом | Обнаружение | Восстановление |
|-------|---------|-------------|----------------|
| **Trace data не поступает** | Pattern miner 0 кандидатов 2+ нед | Cron alert «no candidates» | Проверить SC.037 writer + projection (см. SC.037 режимы) |
| **Reject-rate >60%** | R15 ставит reject на большинство кандидатов | Audit-loop калибровки (monthly) | Пересмотр алгоритма кластеризации; tighten similarity threshold |
| **PII в кандидатах** | grep email/uuid в `pattern-miner-W{N}.md` | Security audit (B7.4) | Backfill crypto-shred + явный детектор в майнере |
| **Triggerный алерт при ложноположительных** | TG-алерт на «3+ одинаковых ошибки», но R15 видит — разные случаи | Audit accept/reject триггерных | Калибровать similarity threshold для триггера отдельно от weekly |
| **Cross-correlation пуст** | Кандидаты без `produced_artifacts` | Warning в отчёте + grep | Дозаписать join в SC.037 session-end + backfill |
| **Майнер пропустил очевидный паттерн** | Human видит, что 5+ trace'ов один и тот же провал, но не в отчёте | Manual review раз в месяц | Низкий importance/recency-вес → tuning формулы скора |

## Сценарии использования

### SC.040.1 — Weekly отчёт в Week Close (primary)

**Триггер:** Воскресенье вечер, Week Close, R-вопросник.

**Потребитель:** R1 Стратег (читает отчёт).

**Владелец:** DP.ROLE.050 R33 Старатель паттернов (генерация отчёта).

**Шаги:**
1. Cron / Week Close skill вызывает `iwe miner weekly --week W{N}`.
2. Майнер читает `learning.agent_trace.*` за неделю + `learning.domain_event` за тот же период.
3. Гибридный retrieval кластеризует похожие trace'ы по `(trace_features, outcome_features)`.
4. Топ-3 паттерна (по комбинации частоты × severity × importance) выдаются как кандидаты.
5. Каждый кандидат содержит draft AR.NNN frontmatter + примеры trace_id + cross-correlation.
6. Отчёт сохраняется в `DS-my-strategy/current/inbox/pattern-miner-W{N}.md` (status: pending-review).
7. R1 в R-вопроснике читает отчёт, передаёт R15 кандидатов с пометками «accept / reject / defer / нужны изменения».

**Время отклика:** ≤5 мин на генерацию отчёта; ≤10 мин на чтение R1.

**Ожидаемый результат:** За месяц — 12-15 кандидатов; accept-rate ≥40% (целевой по audit-loop калибровки).

**Симптом пропуска:** Week Close без отчёта 2+ нед → проверить cron / projection lag.

### SC.040.2 — Триггерный алерт на rapid cluster

**Триггер:** 3+ trace'а с одним типом провала в течение 7 дней.

**Потребитель:** R1 Стратег (немедленная реакция).

**Владелец:** TBD R33 + alerter rule.

**Шаги:**
1. Continuous monitor (cron каждые 6h) проверяет recent trace'ы.
2. При detection кластера N≥3 — emit `agent.miner.rapid_cluster` event.
3. TG-алерт + срочный отчёт в `DS-my-strategy/current/inbox/pattern-miner-RAPID-YYYY-MM-DD.md`.
4. R1 либо: (a) принимает кандидат сразу и спускает R15; (b) откладывает до Week Close (если не критично); (c) reject (false alert) → калибровать порог.

**Время:** ≤24h от третьего инцидента до алерта.

**Ожидаемый результат:** Системные регрессии ловятся до накопления критической массы.

**Симптом пропуска:** Через неделю в weekly-отчёте 8 trace'ов на один паттерн, но триггер не сработал → tuning порога.

### SC.040.3 — Аудит при iwe-rules-review (cross-check)

**Триггер:** Monthly iwe-rules-review (review культуры работы IWE).

**Потребитель:** R5 Архитектор (как ревьюер правил).

**Владелец:** R33 + R5.

**Шаги:**
1. `iwe miner audit --since 2026-04-01` — отчёт о работе правил за месяц.
2. Для каждого активного AR.NNN: сколько раз сработало, на каких trace'ах, false-positive rate.
3. Для каждого «мёртвого» AR (не сработало месяц): кандидат на удаление.
4. Для каждой группы 5+ inactive AR в одном домене: знак, что класс правил вышел из обращения.
5. R5 на iwe-rules-review принимает решения по cleanup.

**Время:** ≤10 мин на отчёт, 30 мин на R-вопросник.

**Ожидаемый результат:** Реестр правил остаётся живым; мёртвые удаляются, рабочие подтверждаются.

## Реализующие сервисы (план Ф4 WP-295)

| Сервис | Роль | Триггер | Путь |
|--------|------|---------|------|
| S-TBD-pattern-miner | DP.ROLE.050 R33 Старатель паттернов | weekly cron + manual | `scripts/trace-pattern-miner.py` |
| S-TBD-rapid-cluster-alerter | TBD R33 + alerter | continuous (cron 6h) | rule в alerter config |
| S-TBD-miner-audit | TBD R33 | monthly | `iwe miner audit` CLI |
| S-TBD-retrieval-api | TBD R30 Архивариус + R33 | OnSessionStart + miner | shared (DP.SC.037 §retrieval) |

## Связь с другими обещаниями

- **Uses:** [DP.SC.037](DP.SC.037-agent-trace.md) (источник данных), [DP.SOTA.022](../06-sota/DP.SOTA.022-agent-trace-replay-multipath.md) §3 (pattern mining паттерны), [WP-272] AR.NNN.md (target формат для кандидатов).
- **Feeds:** apply-captures workflow (R15 принимает кандидатов), [DP.SC.024](DP.SC.024-iwe-maintenance.md) R-вопросник (как вход), iwe-rules-review (как audit).
- **Related:** WP-217 capture-bus (комплементарно — real-time детекторы vs batch майнинг).
- **Potentially-conflicts:** нет. SC.040 — recommend-only; финал — за человеком.

---

**Статус:** draft, 17 мая 2026. Реализация: Ф4 WP-295 (~10h). Требует: SC.037 в проде ≥2 нед (накопление trace'ов для калибровки), audit-loop calibration метрик.
