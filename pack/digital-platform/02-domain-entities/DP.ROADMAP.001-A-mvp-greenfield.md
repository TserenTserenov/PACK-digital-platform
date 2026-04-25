---
id: DP.ROADMAP.001-A
name: Neon MVP-greenfield (infra-first, старт 24 апр)
type: roadmap-variant
status: in_progress
valid_from: 2026-04-24
version: 0.6
derived_from: DP.ARCH.004@v2.3
parent: DP.ROADMAP.001-neon-migration
summary: "Параллельный к основному Roadmap план: MVP-greenfield с 9 БД активных из 12 целевых (согласно DP.ARCH.004 §1 v2.3), infra-first. Core-team и волонтёры подключаются по факту готовности инфры, не по календарю."
related:
  realizes: [WP-253]
  uses:
    - DP.SC.020    # Event Ingest обещание
    - DP.SC.101    # LMS Subscription Webhook контракт
    - DP.SC.122    # Rewards Projection обещание
    - DP.ROLE.032  # Event Ingester роль
    - DP.ROLE.034  # Rewards Projector роль
    - DP.ARCH.004  # Карта 12 БД v2.3
  source: "WP-253 Ф9 MVP-greenfield (переработан 24 апр вечером: volunteer-first → infra-first), WP-228 Ф30 sync @v2.3"
created: 2026-04-24
updated: 2026-04-25
---

# Neon MVP-greenfield (infra-first)

> ⚠️ **DRIFT NOTICE (v0.6, 25 апр):** Нумерация фаз Ф9.X в этом документе **не синхронизирована** с фактическим прогрессом WP-253 после ускорения 25 апр (4 gates PASS за день). Source-of-truth для актуальной последовательности фаз и их статусов — context-файл WP-253 в `~/IWE/DS-my-strategy/inbox/WP-253-neon-architecture-implementation.md` § Ф9. Полная rewrite этого документа запланирована на W18 (≥28 апр) после Strategy Session.
>
> **Краткая карта drift'а:**
>
> | Roadmap-A v0.5 | Context-файл (актуальное состояние) |
> |---|---|
> | Ф9.1 (event-gateway+projection) | Ф9.1 (DDL только) + Ф9.2 (event-gateway smoke) + Ф9.3 (projection-worker) — расщеплено |
> | Ф9.2 (Neon БД) | Ф9.1 (включает) + Ф9.1b (dev-branch dry-run) |
> | Ф9.3 (Bridge-1) | post-MVP |
> | Ф9.4 (aist-bot refactor) | Ф9.5 (часть core-team флага) |
> | Ф9.5 (payment-receiver) | post-MVP (вне Ф9 объёма) |
> | Ф9.6 (journal aggregator) | Ф9.9 (post-Ф9.7) |
> | Ф9.7 (indicators calc) | post-MVP |
> | Ф9.8 (projection-worker rewards) | Ф9.3 (уже DONE 25 апр) |
> | Ф9.9 (knowledge-platform shadow) | Ф9.8 (после Ф9.7 reliability gate) |
> | Ф9.10 (personal-guide E2E) | Ф9.5 (часть core-team прогона) |
>
> **Что DONE по факту 25 апр (4 gates PASS):** Ф9.1 DDL → Ф9.1b dev-branch → Ф9.2 event-gateway smoke → Ф9.3 LISTEN/NOTIFY round-trip → Ф9.4 internal smoke G-I4 (10k req @ 1113 rps, p95=138ms, projection 578ms median). Подробности — context-файл WP-253.

> **Переработан 24 апр вечером.** План изменён с volunteer-first (старт 4 мая, entry gate = 5 ory_ids) на infra-first (старт немедленно, волонтёры подключаются по reliability-gate). Причина: нельзя тестировать на несуществующих БД; Red Line 1 мая работает в параллельном ресурсе (content/landing) и не конкурирует с Neon DDL. Подробности — см. WP-253 Ф9 «Переработка 24 апр».
>
> **Дальнейшее ускорение 25 апр:** 4 internal gates Ф9.1-Ф9.4 пройдены за один день (другой инстанс утром + субагент-ревью + production toolchain). MVP узкое место снято на 5 дней раньше плана. Core-team прогон по Ф9.5 — 30 апр – 3 мая. Production deploy event-gateway + projection-worker — 27-29 апр (см. `inbox/WP-253-F9.5-deploy-runbook.md`).

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

## Scope: 12 БД (infra-first, порядок = критический путь)

> **Примечание по датам:** секции «Неделя 1» / «Неделя 2» и даты «4 мая», «10 мая», «17 мая» в исходном тексте ниже — ориентировочные. Реальный старт каждой фазы — по exit-gate предыдущей. Актуальный порядок и бюджеты фаз Ф9.1–Ф9.7 — см. WP-253 Ф9 context файл (инбокс).

### Неделя 1 (4-10 мая): 9 БД активных

| # | БД | Writer (MVP) | Reader (MVP) | Примечание |
|---|-----|--------------|--------------|------------|
| 1 | **persona** | onboarding + captures + IWE-скиллы | /personal-guide-start, Portnoy | Greenfield |
| 2 | **journal** | event-gateway (raw телеметрия) | aggregator → event-gateway | Raw события |
| 3 | **payment** | LMS Aisystant (Дима, read-only для нас) | `subscription` checker | Bridge-1 polling |
| 4 | **subscription** | payment-receiver (наш Worker) | gateway-mcp JWT entitlement | Таблицы: `subscription_grants` + `payment_audit` (mapping hash→external_id). Bridge-2 webhook к Диме — **отложен до ≈17 мая** |
| 5 | **indicators** | calculators (baseline, RCS) | /personal-guide-start, Portnoy | Новая БД, **не** rename digital-twin |
| 6 | **aist_bot** | бот (FSM, chat state) | сам бот | Рефакторинг: events уходят через POST event-gateway |
| 7 | **knowledge-platform** | reindex worker | knowledge-mcp | Split из `knowledge`, **shadow + dual-read 48h** |
| 8 | **reference** | seed (тарифы, qualification_level, payment_kind, event_schemas) | все читатели | Catalog |
| 10 | **learning** | event-gateway (DP.ROLE.032) | projection-worker → rewards | Единая таблица `domain_event` с `source` колонкой |
| 12 | **rewards** | projection-worker (LISTEN/NOTIFY на learning.domain_event) | gateway-mcp `/balance`, /personal-guide | Минимальная схема: `point_balances` + projection-worker (**не** SQL trigger — см. П2 ревью) |

**Событийный pipeline:**
```
Источники ──POST /events──► event-gateway (DP.ROLE.032)
                                     │
                              [buffer on Neon fail]
                                     │
                                     ▼
                         learning.domain_event (INSERT)
                                     │
                           LISTEN/NOTIFY
                                     │
                                     ▼
                    projection-worker (rewards)
                                     │
                                     ▼
                         rewards.point_balances
```

### Неделя 2 (11-17 мая): 3 БД + стабилизация

| # | БД | Действие |
|---|-----|---------|
| 9 | **publication** | content-pipeline MVP (WP-155 Ф0-Ф3) — writer; reader = multichannel-publisher |
| 11 | **community** | Создать пустую БД (placeholder). Writer Q3 в WP-256 (Random Coffee legacy port) |
| — | Стабилизация | Багфиксы по фидбеку 5 волонтёров, onboarding remaining 4, документация |

**Bridge-2 контракт для Димы (DP.SC.101)** — передаётся **в конце Н2** (≈17 мая), после стабилизации. До этого подписки волонтёров живут только у нас, LMS их не видит (core team не оплачивает через LMS в MVP).

## Фазы реализации (Н1)

### День 0 — 2-3 мая (выходные, подготовка)

- Инвентарь существующих клиентов `digital-twin` (dt-mcp, бот sync, collectors) — для feature flag
- Заготовка JSON Schemas для event-gateway (минимум 5 source×event_type)
- PII whitelist per source (reference.event_schemas seed)
- Проверка pg_fdw из Neon к LMS `213.139.211.118` (пробный branch) — fallback если не работает: Cloudflare Worker polling
- Генерация `IWE_LMS_SERVICE_TOKEN` для Bridge-2 (пока хранится у нас)

**Бюджет:** 4h (опционально, пользователь сам решает работать в выходные)

### Ф9.1 — Event-gateway + projection-worker (понедельник 4 мая)

**Артефакты:**
- `DS-MCP/event-gateway/` (Cloudflare Worker): POST /events handler + JSON Schema validation (схемы в `src/schemas/*.json`) + dedup по UNIQUE(source, external_id) + прямая запись в `learning.domain_event` (без outbox для MVP, см. §ArchGate П1).
- `DS-MCP/projection-worker-rewards/` (Python, Railway): LISTEN/NOTIFY на `learning.domain_event_added` → UPDATE `rewards.point_balances`.

**Entry-gate:** DP.SC.020 + DP.ROLE.032 в Pack существуют (✅ 24 апр).

**Exit-gate:** smoke-test 10 событий через curl, все в `learning.domain_event`; idempotency проверен; `/balance` обновляется в ≤1s после события.

**Бюджет:** 12h (event-gateway 6h + projection-worker 6h).

### Ф9.2 — Новые Neon БД (вторник 5 мая, утро)

**Артефакты:**
- Создать в Neon проекте 9 активных БД MVP (подмножество 12 целевых согласно [DP.ARCH.004](DP.ARCH.004-neon-data-architecture.md) §1 v2.3, принцип database-per-BoundedContext): persona, journal, indicators, subscription, reference, knowledge-platform, learning, rewards, payment (foreign-view). БД #10 community, #11 lead, #12 rewards — post-MVP (P5 основного Roadmap).
- DDL миграции в `DS-IT-systems/neon-migrations/mvp/*`
- Seeds для reference (event_schemas, payment_kind, qualification_level, tariffs)

**Exit-gate:** `\dt` в каждой БД показывает ожидаемые таблицы, seeds есть.

**Бюджет:** 10h.

### Ф9.3 — Bridge-1 Cloudflare Worker (вторник 5 мая, вечер)

**Артефакты:**
- `DS-MCP/lms-bridge/` (Cloudflare Worker): polling `lesson_progress`, `payment`, `qualification_level_event` из LMS каждые 15 мин → POST event-gateway с `source='lms-aisystant'`
- Cursor state в `reference.bridge_cursors` (last_seen_id per table)

**Dependency:** Ф9.1 event-gateway готов.

**Exit-gate:** 1 час работы → events в learning.domain_event от LMS.

**Бюджет:** 8h.

### Ф9.4 — Aist-bot refactor (среда 6 мая)

**Артефакты:**
- Заменить INSERT в `aist_bot.events` → POST event-gateway
- Feature flag `NEW_ARCH_USERS=<list>` (5 ory_id волонтёров) — роутит их события в event-gateway, остальных — в старую `aist_bot.events`
- FSM/chat state остаётся в `aist_bot` (не мигрируется)
- `subscription_grants` writer переезжает из `aist_bot` → `subscription` (наша новая БД)

**Exit-gate:** 1 тестовый пользователь (ory_id волонтёра) → event в новой БД. Остальные → в старой.

**Бюджет:** 10h.

### Ф9.5 — Payment-receiver + subscription (среда 6 мая, вечер)

**Артефакты:**
- `subscription.subscription_grants` в Neon #4 — таблица + writer права
- `subscription.payment_audit` в Neon #4 — таблица mapping `external_payment_ref_hash → external_payment_id`. Writer = payment-receiver (INSERT при каждом успешном платеже), reader = служба поддержки (SELECT при запросе от Димы по хэшу). Маркер О (объект-аудит, immutable). Колонки: `id`, `external_payment_ref_hash`, `payment_system`, `external_payment_id`, `subscription_grant_id` (FK), `created_at`. Создаётся в Ф9.2 DDL.
- Payment-receiver Cloudflare Worker пишет в обе таблицы на YooKassa callbacks
- `subscription.subscription_grants.source='iwe-payment-receiver'` (для future Bridge-2)
- **Seed-скрипт `DS-MCP/event-gateway/seeds/volunteers-subscription-grants.sql`** — INSERT 5 строк в `subscription_grants` для волонтёров: `valid_from=2026-05-04`, `valid_until=2026-08-04`, `type='pack_digital_platform'`, `source='manual-seed-mvp'`. Без grant'а gateway-mcp JWT не вернёт entitlement → `/personal-guide-start` не запустится. Запуск скрипта = exit-gate Ф9.5.

**NB:** Bridge-2 webhook к Диме **в этой фазе не поднимается**. Волонтёры уже подписаны через LMS, MVP не требует синхронизации обратно. Payment-receiver пишет **только в `subscription_grants`** — исходящий webhook-клиент к Диме реализуется отдельной фазой **после** активации endpoint'а Димы (≈после 17 мая).

**Exit-gate:** (а) тестовая YooKassa-транзакция → grant в subscription; (б) seed-скрипт выполнен → 5 grant-строк существуют (`SELECT count(*) FROM subscription.subscription_grants WHERE source='manual-seed-mvp'` = 5).

**Бюджет:** 5h (4h payment-receiver + 1h seed-скрипт).

### Ф9.6 — Aggregator journal → event-gateway (четверг 7 мая)

**Артефакты:**
- `DS-MCP/journal-aggregator/` (Cloudflare Worker или cron): читает `journal.event` raw → семантическая свёртка (например, `slot_logged` = WakaTime heartbeats за 5 мин) → POST event-gateway с `source='journal-aggregator'`
- Rules в `reference.aggregation_rules`

**Exit-gate:** 1 час WakaTime events → derived event в learning.

**Бюджет:** 6h.

### Ф9.7 — Indicators calculators (четверг 7 мая, вечер)

**Артефакты:**
- Baseline calculator: читает `learning.domain_event` за последние 30д → обновляет `indicators.baseline` per user_id
- RCS calculator: те же события → `indicators.rcs_current`
- Cron раз в сутки (ночь)

**Exit-gate:** у волонтёра с 5+ events → baseline не null.

**Бюджет:** 4h.

### Ф9.8 — Projection-worker rewards (пятница 8 мая)

**Артефакты:**
- `DS-MCP/projection-worker-rewards/` (Cloudflare Worker или Python process на Railway)
- Читает NOTIFY на `learning.domain_event` → инкрементирует `rewards.point_balances` по правилам из `reference.reward_rules`
- Idempotent replay: при рестарте начинает с `last_processed_id`

**Exit-gate:** событие `lesson_completed` от Bridge-1 → баланс волонтёра +N баллов.

**Бюджет:** 6h.

### Ф9.9 — Knowledge-platform shadow + dual-read (суббота 9 мая)

**Артефакты:**
- Shadow copy knowledge → knowledge-platform (новая БД)
- Reindex через существующий worker
- 48h dual-read: knowledge-mcp читает из обеих; parity check
- Cut-over feature flag после 48h

**NB:** Это самая рискованная фаза — трогает live knowledge-mcp (SC-112). Возможен откат на старую knowledge, если parity fail.

**Exit-gate:** parity 100% за 48h + smoke-test поиска у 5 волонтёров.

**Бюджет:** 6h active + 48h наблюдение.

### Ф9.10 — Personal-guide + integration smoke (воскресенье 10 мая)

**Артефакты:**
- `/personal-guide-start` переключается на читать `persona` + `indicators` из новых БД (feature flag per user)
- End-to-end smoke-test: волонтёр запускает `/personal-guide-start` → persona читается → indicators читаются → события capture → event-gateway → learning → rewards

**Exit-gate:** 1 волонтёр прошёл E2E без ошибок.

**Бюджет:** 6h.

## Фазы Н2 (11-17 мая)

- **Ф9.11:** Volunteer onboarding — остальные 4 core team подключаются, фидбек, багфиксы (8h).
- **Ф9.12:** publication MVP (WP-155 координация, основной труд в WP-155).
- **Ф9.13:** community placeholder БД (0.5h).
- **Ф9.14:** Передача DP.SC.101 Диме + календарная координация запуска endpoint'а (1h).
- **Ф9.15:** Реопен WP-228 Ф28 для патча DP.ARCH.004 v2.3 — добавить event-gateway в §8 Writers (2h).
- **Ф9.16:** PROCESSES.md для event-gateway + Bridge-1 + projection-workers (3h).

## Бюджет (сводный)

| Фаза | Бюджет |
|------|--------|
| День 0 опционально | 4h |
| Ф9.1 event-gateway + buffer + projection | 12h |
| Ф9.2 9 БД create + schemas + seeds | 10h |
| Ф9.3 Bridge-1 Worker | 8h |
| Ф9.4 aist-bot refactor | 10h |
| Ф9.5 payment-receiver + subscription + payment_audit + seed | 5h |
| Ф9.6 journal aggregator | 6h |
| Ф9.7 indicators calculators | 4h |
| Ф9.8 projection-worker rewards | 6h |
| Ф9.9 knowledge shadow + dual-read | 6h active |
| Ф9.10 personal-guide + E2E smoke | 6h |
| **Н1 ИТОГО** | **~77h** (~11h/день при 7 днях) |
| Ф9.11-9.16 Н2 | ~15h |
| **ВСЕГО Н1+Н2** | **~92h за 14 дней** |

**Cut-line при отставании (что режется первым):**
1. Ф9.9 knowledge-platform — откладывается до основного Roadmap (knowledge-mcp продолжает читать старую `knowledge`).
2. Ф9.6 journal aggregator — derived events можно не делать в MVP (rewards будет учитывать только события прямых источников).
3. Ф9.14 Bridge-2 контракт передача — уже отложена до конца Н2.

**Не режутся (мандат):**
- Ф9.1 event-gateway, Ф9.2 БД, Ф9.4 aist-bot feature flag, Ф9.5 subscription, Ф9.8 rewards, Ф9.10 E2E — без них нет MVP.

## Риски

| Риск | Вероятность | Эффект | Митигация |
|------|-------------|--------|-----------|
| **R-A1** | pg_fdw Neon→LMS не работает | Средняя | Bridge-1 не запускается | Fallback на Cloudflare Worker polling (проверено в WP-183) |
| **R-A2** | LMS схема меняется в Н1 | Низкая | Bridge-1 теряет данные | Дима предупреждён (задача на ИТ-встрече 4 мая) + schema_version cursor |
| **R-A3** | Event-gateway bottleneck 50 rps | Низкая | POST latency > 500ms | Buffer (Cloudflare Queues) + 202 Accepted; масштабирование автоматом CF |
| **R-A4** | Knowledge reindex parity fail | Средняя | Ф9.9 откат, cut-line | Shadow + dual-read 48h даёт защиту; worst case — режем фазу |
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

### Шаг 4. Откат knowledge-platform dual-read (если Ф9.9 активна)

- Снять dual-read флаг в knowledge-mcp → читает только старую `knowledge`.
- Shadow-индекс новой БД оставить (для повторной попытки).

### Шаг 5. Post-mortem (≤48h после rollback)

- Запись инцидента в `DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.3.Operations/Incidents/`.
- Решение: чинить и продолжать MVP с задержкой / отложить MVP до следующего окна / переходить на основной DP.ROADMAP.001 без MVP-greenfield (acceptable если G-A8 fail).

### Что не покрыто rollback'ом

- Bridge-1 LMS→нас продолжит работать (он pull'ит из LMS вне зависимости от feature-flag). Если нужен полный stop — отключить Cloudflare Worker Bridge-1 (deploy с пустым cron).
- Если волонтёр успел получить runtime-материалы из `/personal-guide-start` (PACK-personal в его GitHub) — они остаются у пользователя. Это **фича, не баг** (Персона = его собственность).

## Entry gate для Ф9 MVP-greenfield (v2, infra-first)

**Entry** (что нужно до старта Ф9.1 DDL):

- [x] Read-only credentials LMS подтверждены (memory/reference_lms_db.md, 8 апр)
- [x] DP.SC.020 Event Ingest создан
- [x] DP.ROLE.032 Event Ingester создан
- [x] DP.SC.101 LMS Webhook контракт (draft, передача отложена до reliability-gate)
- [x] **ArchGate ЭМОГССБ v3 для event-gateway ПРОХОДИТ (24 апр)** — 2⚠️ (Скорость, Безопасность) с митигациями; пересмотр если smoke-test p95 >1s или B2.1 Secrets Inventory не закрыт
- [x] Feature flag механизм принят: `reference.feature_flags` (generic таблица БД #8)
- [x] Buffer решение принято: прямая запись без outbox для MVP; CF Queues поверх — Phase 2

**Internal gate** (перед подключением волонтёров, Ф9.7):

- [ ] Список ory_id первой волны волонтёров (≥2, не блокирует старт DDL) — собирается во время Ф9.5/Ф9.6

## Exit gate (полный MVP принят)

- [ ] 5 волонтёров запустили `/personal-guide-start` на новой схеме
- [ ] 5 волонтёров видят `/balance` с баллами за неделю
- [ ] Bridge-1 тянет lesson_progress из LMS 7 дней без разрывов
- [ ] event-gateway p95 ≤500ms при пиковой нагрузке
- [ ] 0 PII-утечек в learning.domain_event (проверка `security.reject_log` 7 дней)
- [ ] Parity check knowledge-platform ≥99.5% (если Ф9.9 не отменена)
- [ ] DP.SC.101 передан Диме с согласованной датой запуска LMS endpoint'а

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

## Related

- **Родительский Roadmap:** DP.ROADMAP.001 (полная миграция, май-август)
- **РП:** WP-253 Ф9 MVP-greenfield
- **Карта БД:** DP.ARCH.004 v2.2 (будет патч v2.3 в Ф9.15)
- **Event model:** DP.SC.020 + DP.ROLE.032
- **Bridge-2 контракт:** DP.SC.101
- **Архитектурные принципы:** DP.ARCH.001 §4 (ЭМОГССБ) + §7 (25 принципов)
