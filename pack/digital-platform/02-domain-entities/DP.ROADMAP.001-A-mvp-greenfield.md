---
id: DP.ROADMAP.001-A
name: Neon MVP-greenfield (infra-first, старт 24 апр)
type: roadmap-variant
status: in_progress
valid_from: 2026-04-24
version: 0.7
derived_from: DP.ARCH.004@v2.4
parent: DP.ROADMAP.001-neon-migration
summary: "Параллельный к основному Roadmap план: MVP-greenfield на 12 целевых БД (DP.ARCH.004 v2.4), infra-first. Cut-over W18 executed 26-27 апр. Ф9.1-Ф9.4 internal gates PASS, Ф9.5 core-team prep активен, Ф9.6-Ф9.8 запланированы. Нумерация Ф9.X выровнена с context-файлом WP-253."
related:
  realizes: [WP-253]
  uses:
    - DP.SC.020    # Event Ingest обещание
    - DP.SC.101    # LMS Subscription Webhook контракт
    - DP.SC.122    # Rewards Projection обещание
    - DP.SC.123    # Platform Observability (WP-244 Ф3b LIVE)
    - DP.ROLE.032  # Event Ingester роль
    - DP.ROLE.034  # Rewards Projector роль
    - DP.ARCH.004  # Карта 12 БД v2.4
  source: "WP-253 Ф9 MVP-greenfield (переработан 24 апр: volunteer-first → infra-first; ускорен 25 апр: 4 internal gates за день; cut-over W18 26-27 апр), WP-228 Ф30 sync @v2.4"
created: 2026-04-24
updated: 2026-04-27
---

# Neon MVP-greenfield (infra-first)

> **v0.7 changelog (27 апр):** drift notice убран; нумерация Ф9.X выровнена с context-файлом WP-253. Cut-over W18 (Ф11.0-Ф11.16, 26-27 апр) acknowledged: 4 legacy БД + 6 schemas в `platform` DROPPED, 12 целевых БД LIVE, WP-269 read-path migrated, WP-244 Ф3b lag-метрики LIVE, knowledge-mcp без legacy fallback. Бюджет пересчитан под фактический ход; entry/exit gates обновлены.

> **Эволюция плана:**
> - **24 апр** — переработка volunteer-first → infra-first (нельзя тестировать на несуществующих БД).
> - **25 апр** — 4 internal gates (Ф9.1-Ф9.4) пройдены за день через multi-instance (другой инстанс + субагент-ревью + production toolchain). MVP узкое место снято на 5 дней раньше плана.
> - **26-27 апр** — cut-over W18 executed: ~36h aggregate (план 168h календарных, сжатие 4.7×). 4 legacy БД DROPPED + 6 schemas в `platform` DROPPED + WP-269 bot read-path migrated + WP-244 Ф3b LIVE.
> - **30 апр – 3 мая** — Ф9.5 core-team прогон (5 чел: Дима, Ильшат, Наталья, Инга, Паша).
> - **4-6 мая** — Ф9.6 reliability gate (48-72h наблюдения).
> - **7-15 мая** — Ф9.7 волонтёрский пилот.
> - **≥18 мая** — Ф9.8 knowledge-platform shadow.

## Контекст и разделение с DP.ROADMAP.001

**Основной Roadmap (DP.ROADMAP.001)** — полная миграция всей экосистемы на 12-БД архитектуру за 4 месяца (май-август). Фазы P0-P7, все пользователи мигрируют, cut-over с parity window 14d + read-only 30d.

**Этот вариант A (greenfield MVP)** — компактный спринт для **5 волонтёров**, параллельный основному. Отличия:

| Критерий | DP.ROADMAP.001 (основной) | DP.ROADMAP.001-A (MVP-greenfield) |
|---------|---------------------------|-----------------------------------|
| Аудитория | Все пользователи (~5000) | Core team (Дима, Ильшат, Наталья, Инга, Паша) → затем волонтёры |
| Длительность | 4 месяца | ~2-3 недели (infra-first, по готовности) |
| Dual-write | Да, 14 дней parity | Нет, greenfield |
| Трогает прод-БД | Да (rename digital-twin, split knowledge) | Нет, новые БД с нуля |
| Cut-over | Per-БД с rollback L1-L4 | Feature flag в боте по user_id |
| Goal | Полная миграция | `/personal-guide-start` + баллы работают на новой схеме у core-team, затем волонтёров |

**После успеха MVP-A:** основной Roadmap использует новую архитектуру как целевое состояние, но остальные пользователи мигрируют по основному плану постепенно.

## Scope: 12 целевых БД (post-cutover state)

> **Состояние на 27 апр Пн вечер:** 12 целевых БД LIVE (DP.ARCH.004 v2.4). Cut-over W18 executed 26-27 апр (Ф11.0-Ф11.16). Legacy DROPPED: `digitaltwin`, `neondb`, `aist_bot`, `directus` (26 апр 16:59); 6 schemas в `platform` (`audit`, `points`, `sync_state`, `knowledge`, `concept_graph`, `health` — 27 апр); `platform.public.users` + `platform.development.user_state` остаются hold-out для FSM (отдельный WP).

| # | БД | Writer | Reader | Статус |
|---|-----|--------|--------|--------|
| 1 | **persona** | onboarding + captures + IWE-скиллы + ETL backfill | /personal-guide-start, Портной, bot WP-269 | ✅ LIVE (post-cutover) |
| 2 | **journal** | event-gateway (raw телеметрия) | aggregator → event-gateway | ✅ LIVE |
| 3 | **payment** | payment-receiver (наш Worker) | `subscription` checker | ✅ LIVE |
| 4 | **subscription** | payment-receiver | gateway-mcp JWT entitlement, bot WP-269 `has_gateway_access()` | ✅ LIVE. Таблицы: `contract` + `contract_event` (audit-trail). Bridge-2 webhook к Диме отложен до ≈17 мая |
| 5 | **indicators** | calculators (baseline, RCS, potential) | /personal-guide-start, Портной | ✅ LIVE. Структура: `calculated_profile` + `indicator_history`. **Не** rename digital-twin (новая БД greenfield) |
| 6 | **learning** | event-gateway (DP.ROLE.032) | projection-worker → rewards, bot WP-269 `was_notification_sent()` | ✅ LIVE. Единая таблица `domain_event` с `source` + `external_id` UNIQUE для idempotency |
| 7 | **rewards** | projection-worker (LISTEN/NOTIFY на learning.domain_event) | gateway-mcp `/balance`, /personal-guide | ✅ LIVE. `point_balances` + `processed_events` (cursor) + projection-worker (**не** SQL trigger) |
| 8 | **reference** | seed (тарифы, qualification_level, payment_kind, event_schemas, reward_rules, feature_flags) | все читатели | ✅ LIVE. Catalog |
| 9 | **knowledge** | reindex worker (knowledge-mcp deploy `84ba0ef2`) | knowledge-mcp | ✅ LIVE. `knowledge_chunk` (post-cutover Ф11.12). Knowledge-platform shadow для Aisystant LMS contents запланирован Ф9.8 ≥18 мая |
| 10 | **health** | event-gateway emit `event_latency_ms`, projection-worker emit `projection_lag_ms`, status-page monitors | Better Stack composite, manual SQL для Ф9.6 gate | ✅ LIVE (WP-244 Ф3b commits `4bf36d4`/`6ea8a74`). `internal_metrics` (EAV) + `graph_usage_events` |
| 11 | **platform** (hold-out) | bot FSM | bot FSM | ⚠️ HOLD-OUT. `public.users` + `development.user_state` остаются до отдельного WP «Bot FSM state migration». `audit`/`points`/`sync_state`/`knowledge`/`concept_graph`/`health` schemas DROPPED 27 апр |
| 12 | **payment-registry** | autopay tokens (B7.3 PII) | payment service | ✅ LIVE (Ф11.5) |

**Событийный pipeline (post-cutover):**
```
Источники ──POST /events──► event-gateway (DP.ROLE.032, CF Worker)
                                     │
                              emit event_latency_ms → health.internal_metrics
                                     │
                                     ▼
                         learning.domain_event (INSERT)
                                     │
                           LISTEN/NOTIFY domain_event_added
                                     │
                                     ▼
                    rewards-projection-worker (Railway, Python)
                                     │
                              emit projection_lag_ms → health.internal_metrics
                                     │
                                     ▼
                         rewards.point_balances
                         rewards.processed_events (cursor)
```

**Mutual read-only:** `aisystant` LMS остаётся через Bridge-2 events poller (events идут в новую `learning.domain_event` через event-gateway).

## Фазы реализации (выровнены с context-файлом WP-253)

> **Source-of-truth для статусов:** `~/IWE/DS-my-strategy/inbox/WP-253-neon-architecture-implementation.md`. Этот документ — Pack-зеркало (стабильные обещания + arch-решения), context-файл — рабочий журнал (Ф11 living-document).

### Ф9.1 — DDL 9 БД (✅ DONE 24 апр)

**Артефакты:** DDL-миграции в `DS-IT-systems/neon-migrations/mvp/*` (`002-learning`, `003-rewards`, `004-reference` + 100-seeds-reference). 9 активных БД из 12 целевых: `learning`, `reference`, `rewards` (Ф9 deploy 25 апр) + дальше per Phase 1: `persona`, `journal`, `indicators`, `subscription`, `payment`, `knowledge`, `health`.

**Exit-gate:** `\dt` показывает ожидаемые таблицы; seeds 11/4/3/3/2/0 (qualification_level / payment_kind / tariffs / event_schemas / reward_rules / feature_flags).

**Бюджет:** ~5h.

### Ф9.1b — Dev-branch dry-run (✅ DONE 24 апр)

**Артефакты:** Применить DDL на отдельный dev-branch Neon, проверить идемпотентность (`drop-and-redeploy`), убедиться что пулёр работает (LISTEN/NOTIFY НЕ работает через `-pooler`, нужен unpooled endpoint — урок).

**Exit-gate:** dev-branch успешно проходит deploy + cleanup без ручных правок.

**Бюджет:** ~2h.

### Ф9.2 — Event-gateway integration smoke (✅ DONE 25 апр, Pack: DP.SC.020 + DP.ROLE.032)

**Артефакты:**
- `DS-MCP/event-gateway/` (Cloudflare Worker): POST /events handler + JSON Schema validation (схемы в `src/schemas/*.json`) + dedup по UNIQUE(source, external_id) + прямая запись в `learning.domain_event` (без outbox для MVP, см. §ArchGate П1)
- 5 source×event_type схем минимум

**Entry-gate:** Ф9.1 DDL applied на `learning`.

**Exit-gate:** 5/5 smoke-tests passed (POST event, idempotency, PII reject, schema reject, /health).

**Бюджет:** ~6h.

### Ф9.3 — LISTEN/NOTIFY round-trip rewards-projection-worker (✅ DONE 25 апр, Pack: DP.SC.122 + DP.ROLE.034)

**Артефакты:**
- `DS-IT-systems/rewards-projection-worker/` (Python, Railway peaceful-vision): LISTEN/NOTIFY на `learning.domain_event_added` → UPDATE `rewards.point_balances` по правилам из `reference.reward_rules` (НЕ SQL trigger)
- Idempotent replay: cursor `rewards.processed_events` (single-row per projection с `last_event_id` + `last_processed_at`)

**Exit-gate:** event POST → balance update в ≤1s; cleanup OK.

**Бюджет:** ~6h.

### Ф9.4 — Internal smoke G-I4 (✅ DONE 25 апр)

**Артефакты:** Load-test scenarios:
- 10k req @ 1113 rps over 10s
- p95=138ms (target ≤500ms)
- projection 578ms median (target ≤1s)
- PII reject 7/7 cases
- idempotency = 1 event accepted, 9999 deduplicated

**Exit-gate:** все 4 метрики выше threshold.

**Бюджет:** ~3h (включая cleanup smoke artifacts).

### Cut-over W18 (Ф11, ✅ DONE 26-27 апр, ~36h aggregate, сжатие 168h→36h = 4.7×)

**Контекст:** между Ф9.4 (internal gates PASS) и Ф9.5 (core-team прогон) сделан cut-over всех writers/readers со старой архитектуры на новую — booster под зонтиком WP-268 + child WP-269 + child WP-270.

| Что | Статус | Когда | Detail |
|-----|--------|-------|--------|
| 4 legacy БД DROPPED | ✅ | 26 апр 16:59 | `digitaltwin`, `neondb`, `aist_bot`, `directus` (backups в archive) |
| 6 schemas в `platform` DROPPED | ✅ | 27 апр | `audit`, `points`, `sync_state` (orphan); `knowledge`, `concept_graph`, `health` (migrated) |
| knowledge-mcp без legacy fallback | ✅ | 27 апр | deploy `84ba0ef2` |
| WP-269 bot read-path migrated | ✅ | 27 апр | 3 readers (`get_aisystant_id`, `was_notification_sent`, `has_gateway_access`) + 4 lazy pools (PERSONA_URL, SUBSCRIPTION_URL, INDICATORS_URL, LEARNING_URL) |
| WP-244 Ф3b lag-метрики LIVE | ✅ | 27 апр | event-gateway `4bf36d4` + rewards-projection-worker `6ea8a74` |
| WP-270 ArchGate v4 PASS | ✅ | 27 апр | Variant 1 (multi-domain rules-based) с future-proof spec для V2 распила |
| Smoke @aist_me_bot prod | ✅ | 27 апр | `/start`, `/profile`, `/aisystant`, `/link` PASS |

**Hold-out:** `platform.public.users` + `platform.development.user_state` (FSM state — отдельный WP).

### Ф9.5 — Core-team MVP прогон (🔴 PREP, 30 апр – 3 мая)

**Артефакты:**
- 5 строк в `reference.feature_flags` (`flag_name='mvp_greenfield'`, 5 ory_user_id core-team, `enabled=true`)
- TG-сообщение в ИТ-чат (см. [`WP-253-F9.5-core-team-tg-sync.md`](../../../../DS-my-strategy/inbox/WP-253-F9.5-core-team-tg-sync.md))
- Тех.инструкция (см. [`WP-253-F9.5-personal-guide-instruction.md`](../../../../DS-my-strategy/inbox/WP-253-F9.5-personal-guide-instruction.md))

**Pre-flight (см. [`WP-253-F9.5-deploy-runbook.md`](../../../../DS-my-strategy/inbox/WP-253-F9.5-deploy-runbook.md)):**
- 🔴 § 0 BLOCKING — verify-backfill-wp253.sql PASS (persona.ory_identity COUNT >= legacy aist_bot.users COUNT, % с telegram_id ≥ 95%, SMOKE для 5+ users → all found). FAIL → cherry-pick `ff1d6dd` из pilot в new-architecture
- § 1 — health checks (event-gateway /health, projection-worker running, БД endpoints), Tseren UUID lookup в `persona.ory_identity`, smoke 1 event, failure-mode F1-F6

**Exit-gate:** ≥3 человека прошли E2E `/personal-guide-start` без помощи + ≥3 ненулевых баланса в `rewards.point_balances` + ≥3 PR/комментария фидбека + 0 потерянных событий.

**Бюджет:** ~6-8h за 4 дня каждый core-team участник + ~1.5h Tseren pre-flight + ~30 мин TG-отправка.

### Ф9.6 — Reliability gate (4-6 мая, 48-72h наблюдения)

> Полная спецификация: [`WP-253-F9.6-reliability-gate.md`](../../../../DS-my-strategy/inbox/WP-253-F9.6-reliability-gate.md).

**Запуск 4 мая утром, оценка 6 мая утром.** 4 binary criteria (conjunctive screening):

1. **Zero data loss** — все события сохранены или явно отброшены, cursor догнал
2. **0 ложных reject** — все unique reasons в `learning.security_reject_log` объяснимы
3. **p95 стабилен** — event-gateway p95 ≤ 500ms каждый час из 48-72h окна (Primary path: `health.internal_metrics` через WP-244 Ф3b)
4. **Projection lag ≤ 10 min 99%** (Primary path: `metric_name='projection_lag_ms'`)

**Pre-flight B4:** WP-244 Ф3b LIVE → Primary path активен по умолчанию (B4 упрощён vs предыдущая версия).

**B1 Load-tests:** L1 повтор G-I4 + L2 sustained low-rate (1 event/мин × 60 мин).

**Exit-gate:** все 4 criteria PASS → открыть Ф9.7. Любой FAIL → откат на фикс.

**Бюджет:** ~1h активного труда + 48-72h пассивного наблюдения.

### Ф9.7 — Волонтёрский пилот (7-15 мая)

**Артефакты:**
- Расширение `feature_flags` на ≥5 волонтёров
- Документация для волонтёров (адаптированная из core-team инструкции)
- Багфиксы по фидбеку core-team (carry-over из Ф9.5)

**Entry-gate:** Ф9.6 PASS на всех 4 criteria.

**Exit-gate:** ≥5 волонтёров запустили `/personal-guide-start`; ≥1 critical bug найден и пофикшен; D7 retention ≥ 60%.

**Бюджет:** ~10-15h Tseren (onboarding + поддержка) + ~5-8h каждый волонтёр.

### Ф9.8 — Knowledge-platform shadow (≥18 мая)

**Артефакты:**
- Shadow copy knowledge-mcp pipeline → новая БД (если нужно отделить от существующей `knowledge`)
- 48h dual-read parity check
- Cut-over feature flag после parity 100%

**Entry-gate:** Ф9.7 экспозиция стабильна (≥7 дней без critical incidents).

**Exit-gate:** parity 100% за 48h + smoke-test поиска у волонтёров.

**Бюджет:** ~6h active + 48h наблюдение.

## Фазы post-MVP (Q2-Q3)

- **Aggregator journal → event-gateway** (~6h): семантическая свёртка `journal.event` raw → derived events (slot_logged etc.)
- **Indicators calculators** (~4h): baseline / potential / RCS calculators per user_id (cron раз в сутки)
- **Volunteer wave 2** (~10h): остальные 4-7 волонтёров после стабилизации
- **publication БД + content-pipeline MVP** (~10h): WP-155 координация
- **community placeholder БД + writer** (~10h): Q3 в WP-256 (Random Coffee legacy port)
- **DP.SC.101 передача Диме + Bridge-2 webhook actвация** (~3h)
- **3 архитектурных пробела WP-269** (Ф11.11, см. context-file):
  - FSM state target (`platform.public.users` + `development.user_state` hold-out → `bot_state` БД или event-sourcing)
  - Q&A history content (qa_query payload metadata-only после DROP `aist_bot.qa_history` — backup restore или payload расширение)
  - GitHub connections storage (после DROP `aist_bot.github_connections` — `secrets` БД для OAuth tokens)

## Бюджет (сводный, выровнен с фактическим ходом)

| Фаза | Статус | Бюджет факт/план |
|------|--------|------------------|
| Ф9.1 DDL 9 БД | ✅ DONE 24 апр | ~5h |
| Ф9.1b Dev-branch dry-run | ✅ DONE 24 апр | ~2h |
| Ф9.2 Event-gateway integration smoke | ✅ DONE 25 апр | ~6h |
| Ф9.3 LISTEN/NOTIFY rewards-projection-worker | ✅ DONE 25 апр | ~6h |
| Ф9.4 Internal smoke G-I4 | ✅ DONE 25 апр | ~3h |
| Cut-over Ф11 (W18) | ✅ DONE 26-27 апр | **~42h aggregate факт ~36h** (multi-instance + 17 субагентов на WP-268) |
| Ф9.5 Core-team prep + прогон | 🔴 PREP, прогон 30 апр – 3 мая | ~1.5h Tseren pre-flight + ~6-8h каждый × 5 = ~30-40h core-team aggregate |
| Ф9.6 Reliability gate | 📅 4-6 мая | ~1h active + 48-72h passive |
| Ф9.7 Волонтёрский пилот | 📅 7-15 мая | ~10-15h Tseren + ~5-8h каждый × ≥5 |
| Ф9.8 Knowledge-platform shadow | 📅 ≥18 мая | ~6h active + 48h наблюдение |
| **Ф9.1-Ф9.4 + Cut-over (DONE)** | | **~64h aggregate** (~17h физ Tseren) |
| **Ф9.5-Ф9.8 (план)** | | **~50-70h aggregate** + passive окна |

**Сжатие vs первоначальный план v0.5:** v0.5 закладывал ~92h за 14 дней. Факт Ф9.1-Ф9.4 + Cut-over = ~64h за 4 дня (24-27 апр). Multi-instance + субагентское ускорение реально работает.

**Cut-line при отставании (что режется первым):**
1. Ф9.8 knowledge-platform shadow — откладывается до Q3.
2. post-MVP aggregator (journal → derived events) — derived events можно не делать в MVP.
3. DP.SC.101 Bridge-2 webhook к Диме — уже отложен до ≥17 мая.

**Не режутся (мандат):**
- Ф9.5 core-team прогон, Ф9.6 reliability gate, Ф9.7 волонтёрский пилот — без них MVP не валидирован.

## Риски

| Риск | Вероятность | Эффект | Митигация |
|------|-------------|--------|-----------|
| **R-A1** | pg_fdw Neon→LMS не работает | Средняя (sn) | Bridge-2 events poller не запускается | Fallback на Cloudflare Worker polling (проверено в WP-183) — post-cutover Bridge-2 LIVE |
| **R-A2** | LMS схема меняется в Ф9.5+ | Низкая | Bridge-2 теряет данные | Дима предупреждён + schema_version cursor |
| **R-A3** | Event-gateway bottleneck 50 rps | Низкая | POST latency > 500ms | Buffer (Cloudflare Queues) + 202 Accepted; масштабирование автоматом CF |
| **R-A4** | Knowledge reindex parity fail | Средняя | Ф9.8 откат, cut-line | Shadow + dual-read 48h даёт защиту; worst case — режем фазу |
| **R-A5** | PII утечка в payload JSONB | Средняя | Security Gate блок | JSON Schema валидация на ingress (DP.SC.020 критерий приёмки) |
| **R-A6** | Feature flag сбой → волонтёрские events в старую БД | Низкая | MVP не видит события | Логирование роутинга в aist-bot + alerting при 0 events в новой БД за 1h |
| **R-A7** | Projection-worker отстаёт от event-gateway | Низкая | Баллы обновляются медленно | Lag-метрика (ingested_at - balance.updated_at) + алерт > 10 мин |

## Rollback playbook MVP

> **Триггер:** обнаружен критический сбой в окне 4-17 мая, требующий вернуть волонтёров на legacy. Сценарии: data corruption в `learning.domain_event`, сломанный rewards projection, неверный entitlement, утечка PII.
> **Принцип:** rollback по feature-flag = моментальный, очистка состояния = асинхронная.

### Шаг 1. Cut traffic — feature-flag off (≤2 мин)

Установить флаг на off для всех 5 волонтёрских `ory_id`:

```sql
UPDATE reference.feature_flags
SET enabled = FALSE
WHERE flag_name = 'mvp_greenfield' AND ory_user_id IN (<5 uuids>);
```

Эффект: aist-bot middleware на следующем запросе видит `enabled=false` → возвращает пользователя на legacy-путь (`aist_bot.public`).

**Верификация:** `SELECT count(*) FROM learning.domain_event WHERE user_id IN (5 ory_ids) AND ingested_at > NOW() - INTERVAL '5 min'` должен идти на 0 в течение 5 мин после cut.

### Шаг 2. Восстановить волонтёров на legacy (≤15 мин)

Для каждого волонтёра в legacy `aist_bot.public`:
- Проверить, что бот видит подписку через старый путь (LMS `user_subscriptions`).
- Если использовался `subscription_grants` seed (Ф9.5) — он не мешает legacy, но volunteer'ам сообщить «возможен короткий разрыв в баллах».

### Шаг 3. Очистка состояния (асинхронно, ≤24h)

- `DELETE FROM learning.domain_event WHERE user_id IN (5 ory_ids)` — события волонтёров.
- `DELETE FROM rewards.point_balances WHERE user_id IN (5 ory_ids)` — баллы.
- `DELETE FROM subscription.subscription_grants WHERE source='manual-seed-mvp'` — seed-подписки.
- `DELETE FROM persona.profile WHERE ory_id IN (5 ory_ids) AND created_at > '2026-05-04'` — если onboarding создавал записи.
- `DELETE FROM reference.feature_flags WHERE flag_name='mvp_greenfield' AND ory_user_id IN (5 ory_ids)` — снять сам флаг (после Шага 1 он уже `enabled=FALSE`, здесь удаляем строки).

**Не удаляем (сохраняем для post-mortem):**
- `security.reject_log` — следы PII reject'ов на ingress.
- `journal.event` — raw телеметрия волонтёров (полезна для разбора причины — что писал aggregator, что отдавал WakaTime). Хранится 30 дней, после — стандартный retention.
- `subscription.payment_audit` — mapping hash→external_id (нужен для сверки с YooKassa-кабинетом, если rollback связан с платежом).
- `learning.domain_event` от non-volunteer'ов (если случайно попали — расследовать).

### Шаг 4. Откат knowledge-platform dual-read (если Ф9.8 активна)

- Снять dual-read флаг в knowledge-mcp → читает только основную `knowledge` БД (post-cutover уже без legacy fallback).
- Shadow-индекс новой БД оставить (для повторной попытки).

### Шаг 5. Post-mortem (≤48h после rollback)

- Запись инцидента в `DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.3.Operations/Incidents/`.
- Решение: чинить и продолжать MVP с задержкой / отложить MVP до следующего окна / переходить на основной DP.ROADMAP.001 без MVP-greenfield (acceptable если G-A8 fail).

### Что не покрыто rollback'ом

- Bridge-2 events poller LMS→нас продолжит работать (он pull'ит из Aisystant LMS mutual read-only вне зависимости от feature-flag). Если нужен полный stop — отключить Railway service `bridge-2-events-poller`.
- Если волонтёр успел получить runtime-материалы из `/personal-guide-start` (PACK-personal в его GitHub) — они остаются у пользователя. Это **фича, не баг** (Персона = его собственность).

## Entry gate для Ф9 MVP-greenfield (v2, infra-first)

**Entry** (что нужно было до старта Ф9.1 DDL — все ✅ к 24 апр):

- [x] Read-only credentials LMS подтверждены (memory/reference_lms_db.md, 8 апр)
- [x] DP.SC.020 Event Ingest создан
- [x] DP.ROLE.032 Event Ingester создан
- [x] DP.SC.101 LMS Webhook контракт (draft, передача отложена до reliability-gate)
- [x] **ArchGate ЭМОГССБ v3 для event-gateway ПРОХОДИТ (24 апр)** — 2⚠️ (Скорость, Безопасность) с митигациями
- [x] Feature flag механизм принят: `reference.feature_flags` (generic таблица в БД `reference`)
- [x] Buffer решение принято: прямая запись без outbox для MVP; CF Queues поверх — Phase 2

**Internal gates (post-cutover):**

- [x] Ф9.1-Ф9.4 PASS (24-25 апр) — DDL + dev-branch + event-gateway smoke + projection round-trip + G-I4 internal load test
- [x] Cut-over W18 executed (26-27 апр) — 4 БД DROPPED + 6 schemas DROPPED + WP-269 + WP-244 Ф3b LIVE
- [x] WP-270 ArchGate v4 PASS (Variant 1 multi-domain rules-based)
- [ ] **🔴 ETL backfill verify PASS** (verify-backfill-wp253.sql, блокер Ф9.5 — см. WP-253-F9.5-deploy-runbook.md § 0)
- [ ] Список 5 ory_user_id core-team собран (29 апр)
- [ ] Список ory_id первой волны волонтёров (≥5, не блокирует Ф9.5) — собирается во время Ф9.6

## Exit gate (полный MVP принят)

- [ ] 5 core-team прошли Ф9.5 `/personal-guide-start` без помощи + ненулевой баланс
- [ ] Ф9.6 reliability gate PASS на всех 4 criteria
- [ ] ≥5 волонтёров прошли Ф9.7 + D7 ≥ 60%
- [ ] event-gateway p95 ≤500ms при реальной нагрузке (verified в Ф9.6)
- [ ] 0 PII-утечек в `learning.domain_event` за 14 дней (проверка `learning.security_reject_log`)
- [ ] Parity check knowledge-platform ≥99.5% (если Ф9.8 запущен)
- [ ] DP.SC.101 передан Диме с согласованной датой запуска LMS endpoint'а
- [ ] 3 архитектурных пробела WP-269 (FSM state, Q&A history, GitHub connections) — решения зафиксированы в Strategy Session W19+

## ArchGate event-gateway single-writer (24 апр 2026)

> **Решение:** event-gateway (Cloudflare Worker) — единственный writer таблицы `learning.domain_event` в Neon. Все источники (LMS via Bridge-1, aist-bot, MCP-серверы, IWE-скиллы, journal-aggregator) пишут через POST /events. Идемпотентность по UNIQUE(source, external_id). Buffer на отказ Neon. Projection-worker через LISTEN/NOTIFY → rewards.point_balances.
> **Метод:** `/archgate` v3 — профиль ЭМОГССБ (✅/⚠️/❌), conjunctive screening, без агрегатного балла. См. `.claude/skills/archgate/SKILL.md`.

### Критические характеристики (Шаг 1)

Критичны для этого решения: **Безопасность** (single-writer = единая точка ingress для PII-валидации), **Эволюционируемость** (базис для P1-P3 основного Roadmap, 6+ источников). Остальные 6 — вторичны.

### Альтернативы (Шаг 2б, A.19 Lawful Comparison)

- **А1.** Две таблицы `domain_event_lms` + `domain_event_iwe`, два writer'а. **Отвергнуто** (24 апр): нарушает OwnerIntegrity + ломает analytics на едином датасете.
- **А2.** Прямые INSERT'ы из каждого источника через `service_user` Postgres. **Отвергнуто:** размывает OwnerIntegrity (N writer'ов одной таблицы), требует БД-доступ для каждого источника, ломает PII-валидацию на ingress.
- **А3.** Kafka между источниками и Neon. **Отвергнуто:** избыточная инфраструктура для MVP (50 rps max), ломает «open formats», требует команду эксплуатации.

### Профиль ЭМОГССБ (Шаг 2)

| # | Характеристика | Статус | Обоснование |
|---|----------------|--------|-------------|
| **Э** | Эволюционируемость | ✅ | Single writer = одна точка расширения. Новый источник = JSON Schema + строка в whitelist `source`, без БД-миграций. Замена CF Worker → другой runtime: контракт POST /events сохраняется. |
| **М** | Масштабируемость | ✅ | CF Worker stateless, autoscale на edge. MVP 50 rps, прямая запись в Neon c pooled connection — потолок ~1k rps. Bottleneck при росте — Neon pool; митигация (Phase 2) — CF Queues поверх event-gateway как rate-limiter. |
| **О** | Обучаемость | ✅ | Все события в одной таблице с колонкой `source` → analytics, drift-детектор, learning loop на едином датасете. Онбординг нового источника: 1 JSON Schema + ≤50 строк кода. Экзоскелет (автор понимает каждый шаг), не протез. |
| **Г** | Генеративность | ✅ | Сторонние плагины (WP-258 Plugin API L2) пишут через тот же POST /events с per-source JSON Schema — без БД-доступа. Готовая точка расширения экосистемы. |
| **С** | Скорость | ⚠️ | SLO p95 ≤500ms (DP.SC.020 критерий 3). CF Worker edge + pooled Neon: realistically 100-300ms для прямой записи, 202 Accepted для buffer-пути. **Риск:** под пиком buffer добавляет eventual consistency ≤5s — некоторые читатели (баланс баллов) это заметят. **Митигация:** при p95 >1s в smoke-test — пересмотр. |
| **С** | Современность | ✅ | Event-sourcing + projection workers (CQRS, Kafka-style ingest на HTTP). DDD Strategic: единый Aggregate Root. Coupling Model: ports & adapters, knowledge coupling низкое (source-specific schemas). Context Engineering: JSON Schema = явный контракт. |
| **Б** | Безопасность (критическая) | ⚠️ | PII reject на ingress (whitelist в JSON Schema, лог в `security.reject_log`). payment_credentials — отдельный класс, хэш в Bridge-2 (DP.SC.101, DP.ARCH.004 §2 П6.1). Bearer token, single audit point. **Риск:** prompt injection в payload от LLM-источников (скиллы) — JSON Schema `additionalProperties: false` закрывает структурную часть, но не семантику. **Митигация:** в MVP источники под нашим контролем; plugin-источники (WP-258) — после аудита каждого. |
| **П** | Переносимость данных (L2.1) | ✅ | `learning.domain_event` — Postgres + JSONB (открытый формат). `pg_dump` экспортирует целиком, перенос на любой Postgres-совместимый сервис. Нет проприетарного протокола. |

**§Б чеклист безопасности (WP-212 B7.1):** Auth — Bearer token ✅; Authorization — per-source whitelist ✅; identity — `actor_user_id` из JWT, не из body ✅; secrets — CF env, B2.1 обновляется в Ф9.4 ⚠️; PII logging — **запрещено**, reject_log хранит только `reject_reason` без payload ✅; SQL injection — параметризованные запросы через neon-http ✅; шифрование — TLS до Neon by default ✅. Итог §Б: **0 ❌, 1 ⚠️ (secrets inventory)** → Безопасность = ⚠️.

**§С чеклист современности:** Context Engineering (DP.SOTA.002) — per-source JSON Schema = Compress (отсекаем лишние поля) ✅; DDD Strategic (DP.SOTA.001) — BC event-ingest определён, UL (source/event_type/occurred_at) консистентен ✅; Coupling Model (DP.SOTA.011) — knowledge coupling низкое, distance coupling низкое, volatility coupling среднее (JSON Schema эволюционирует) ✅.

### Вето-фильтр (Шаг 3, conjunctive screening)

**Правило 1. Критические характеристики:** Безопасность (критич.) → ⚠️, Эволюционируемость (критич.) → ✅. Критических ❌ нет. → **не сработало.**

**Правило 2. Блокеры (❌):** не найдено. → **не сработало.**

**Правило 3. Соотношение:** ⚠️ Скорость, Безопасность (2 шт.) | ✅ Эволюционируемость, Масштабируемость, Обучаемость, Генеративность, Современность, Переносимость (6 шт.). → **не сработало** (≥4⚠️ не достигнуто, ✅ есть).

**Итог вето-фильтра:** ни одно правило не сработало → решение **проходит**.

### Принципы DP.ARCH.001 §7 (Шаг 0)

| Принцип | Соблюдён? |
|---------|-----------|
| П3 OwnerIntegrity | ✅ один writer на learning.domain_event |
| П7 Idempotency | ✅ UNIQUE(source, external_id) на БД-уровне |
| П11 PII-by-default-deny | ✅ JSON Schema whitelist + reject log |
| П15 Eventual consistency допустима | ✅ 202 Accepted + projection lag ≤5s |
| П19 Open formats | ✅ Postgres JSONB + standard SQL |
| П24 Data Portability | ✅ pg_dump-friendly, vendor-agnostic |

### Решения по ⚠️ (Шаг 5)

1. **Скорость ⚠️ — митигация:** smoke-test p95 на 4 мая (Ф9.3.3 критерий). При p95 >1s — добавить Cloudflare Queues поверх прямой записи (event-gateway → CF Queues → Neon writer).
2. **Безопасность ⚠️ — митигация:** (а) B2.1 Secrets Inventory обновляется в Ф9.4 (event-gateway deploy); (б) plugin-источники (WP-258, Q3) проходят аудит индивидуально; до того event-gateway принимает только источники под нашим контролем.

### Принятые решения (24 апр 2026, после обратной связи)

Все 4 решения закрыты, альтернативы проанализированы и зафиксированы в этом документе.

1. **Buffer механизм** (G-A4): **прямая запись в `learning.domain_event` без outbox**.
   **Причина:** в MVP Neon single-instance, кросс-БД транзакций нет. Outbox-паттерн защищает transactional consistency при кросс-БД проекции — у нас и источник, и приёмник в одной Neon. Для защиты от отказа Neon outbox в Neon не помогает (куда писать, если Neon недоступен?). Плюс: минус одна зависимость, minus транзакционные сложности.
   **Риск:** потеря событий при отказе Neon 2 недели — мал; идемпотентность (UNIQUE source, external_id) позволит переигрывать из source после восстановления.
   **Phase 2 (добавляется при):** кросс-БД проекция (rewards переедет в отдельный инстанс) ИЛИ рост нагрузки ×100 → CF Queues поверх event-gateway.

2. **Projection-worker deployment** (rewards): **Python на Railway + LISTEN/NOTIFY**.
   **Причина:** <1s latency важен для UX — пользователь видит `/balance` сразу после события. CF Worker cron даёт ≥30s. CF Worker не держит persistent Postgres connection.
   **Отвергнуто — Postgres trigger:** логика коэффициентов баллов будет эволюционировать (новые event_type, бонусы за серию, штрафы), бизнес-логику в SQL trigger сложно тестировать и эволюционировать.

3. **JSON Schema location** (DP.SC.020 §86): **в коде event-gateway (TypeScript, `src/schemas/*.json`)** для MVP.
   **Причина:** за 2 недели добавим только 3 источника (Bridge-1 lesson_progress, payment-receiver, IWE-скиллы) — deploy на каждую схему терпимо. Типобезопасность в TS.
   **Phase 2:** перенести в `reference.event_schemas` (БД #8) при появлении plugin-источников (WP-258, Q3) — там hot-reload без deploy критичен.

4. **Feature-flag механизм** (G-A3): **`reference.feature_flags` (generic таблица в БД #8 health)**.
   **Причина:** «волонтёр MVP» — временный флаг на 2 недели, через месяц бесполезен. Класть в `persona.profile` = загрязнять доменную таблицу временным. Generic таблица переиспользуется для будущих A/B и MVP-флагов.
   **Схема:**
   ```sql
   CREATE TABLE reference.feature_flags (
     flag_name TEXT NOT NULL,
     ory_user_id UUID NOT NULL,
     enabled BOOLEAN NOT NULL DEFAULT TRUE,
     valid_until TIMESTAMPTZ,
     created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
     PRIMARY KEY (flag_name, ory_user_id)
   );
   ```
   **Seed для MVP:** 5 строк `('mvp_greenfield', <ory_id>, TRUE, '2026-05-17 23:59:59+00', NOW())`.
   **Reader:** aist-bot middleware + event-gateway (dispatcher new vs legacy пути).
   **Owner (Kind/Role):** reference = catalog/operational — writer = операционная команда (seed-скрипт, migration), не пользователь; owner = health-домен (#8).

### Вердикт ArchGate

**ПРОХОДИТ** — event-gateway single-writer принят как архитектурная основа Ф9 MVP-greenfield и базы для P1-P3 основного DP.ROADMAP.001.

**Триггеры пересмотра (перевод ⚠️ → ❌):**
- Reject rate >5% или утечка PII в `learning.domain_event` (Безопасность → ❌).
- p95 latency >1s в smoke-test без митигации (Скорость → ❌).
- Появление 2-го writer'а в production (нарушение П3 OwnerIntegrity).
- Запрос на bidirectional sync (event-gateway → источник) — переоценка модели.

**Подпись:** Tseren, 2026-04-24, Opus 4.7. Прошёл 3 независимых субагент-ревю (5+3+1 находки до создания, 3 nit'а после). ArchGate перепрошит в v3-формате после обратной связи о score-форме.

## Карта статусов фаз (на 27 апр Пн вечер)

| Фаза | Статус | Когда | Source-of-truth deliverable |
|------|--------|-------|------------------------------|
| Ф9.1 DDL 9 БД | ✅ DONE | 24 апр | `DS-IT-systems/neon-migrations/mvp/00X-*.sql` |
| Ф9.1b Dev-branch dry-run | ✅ DONE | 24 апр | clean apply + cleanup |
| Ф9.2 Event-gateway smoke | ✅ DONE | 25 апр | 5/5 tests passed |
| Ф9.3 Projection LISTEN/NOTIFY | ✅ DONE | 25 апр | round-trip ≤1s |
| Ф9.4 Internal load smoke G-I4 | ✅ DONE | 25 апр | 10k req @ 1113 rps, p95=138ms |
| Cut-over Ф11 (W18) | ✅ DONE | 26-27 апр | 4 БД + 6 schemas DROPPED, knowledge-mcp/bot deployed |
| Ф9.5 Core-team прогон | 🔴 PREP | 30 апр – 3 мая | TG отправлен + ≥3 PASS + ≥3 ненулевых баланса |
| Ф9.6 Reliability gate | 📅 plan | 4-6 мая | 4/4 binary criteria PASS |
| Ф9.7 Волонтёрский пилот | 📅 plan | 7-15 мая | ≥5 волонтёров + D7 ≥60% |
| Ф9.8 Knowledge-platform shadow | 📅 plan | ≥18 мая | parity 100% за 48h |

## Related

- **Родительский Roadmap:** DP.ROADMAP.001 (полная миграция, май-август)
- **РП:** WP-253 Ф9 MVP-greenfield (parent zonтик), WP-268 (booster cut-over Phase 2), WP-269 (read-path migration child), WP-270 (multi-domain projection-worker child), WP-244 Ф3b (observability lag-метрики), WP-254 (миграция учебных объектов post-MVP)
- **Карта БД:** DP.ARCH.004 v2.4 (post-cutover)
- **Event model:** DP.SC.020 + DP.ROLE.032
- **Rewards:** DP.SC.122 + DP.ROLE.034
- **Observability:** DP.SC.123 (WP-244 Ф3b LIVE)
- **Bridge-2 контракт:** DP.SC.101 (отложен ≥17 мая)
- **Архитектурные принципы:** DP.ARCH.001 §4 (ЭМОГССБ) + §7 (25 принципов)
- **Context-файл (рабочий журнал):** [`~/IWE/DS-my-strategy/inbox/WP-253-neon-architecture-implementation.md`](../../../../DS-my-strategy/inbox/WP-253-neon-architecture-implementation.md) — Ф11 living-document, читать снизу вверх (HD #25)
- **Handoff W19+:** [`~/IWE/DS-my-strategy/inbox/WP-253-handoff-W19-plan.md`](../../../../DS-my-strategy/inbox/WP-253-handoff-W19-plan.md)
