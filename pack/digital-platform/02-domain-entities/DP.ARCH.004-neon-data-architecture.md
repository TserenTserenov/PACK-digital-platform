---
id: DP.ARCH.004
name: Архитектура данных Neon (Database-per-Service)
type: domain-entity
status: active
valid_from: 2026-04-14
summary: "7 баз данных в Neon по принципу database-per-service. ERD предметной области по базам + общая диаграмма межбазовых связей. Принята на встрече ИТ 14 апр 2026."
related:
  - DP.SOTA.016   # Database-per-Service SOTA
  - DP.ARCH.003   # Digital Twin Architecture
  - DP.ARCH.001   # Platform Architecture
  - WP-228        # Карта данных Neon
decision_source: "Встреча ИТ 8, 14 апр 2026 (транскрипция Zoom)"
supersedes: "WP-232 решение об одной базе platform"
---

# DP.ARCH.004 — Архитектура данных Neon

> **Это целевая архитектура.** Текущее физическое состояние — одна база `platform` (WP-232). Разделение на отдельные базы — следующий РП.

## Принципы (решены 14 апр 2026)

**П1. 1 сервис = 1 база** (не схема)
Каждый сервис имеет собственную БД с собственными credentials. Другие сервисы не ходят в неё напрямую — только через API.

**П2. FK только внутри одной базы**
Ссылки между сервисами — только `ory_id`/`telegram_id` без FK constraint. Консистентность через API или Saga.

**П3. Схема = namespace для роли-администратора**
Внутри базы схемы разграничивают доступ ролей (финансист видит `finance.*`). Не для изоляции сервисов.

**П4. `ory_id` — глобальный ключ**
Каждая база хранит `ory_id` как обычную колонку без FK. Ory Kratos — source of truth идентичности. `user_identities` хранит только то, чего Ory не знает: `telegram_id`, `lms_id`, `region`.

**П5. Activity Hub — проектировать под замену**
Events — кандидат на ClickHouse/TimescaleDB при масштабировании. Именно поэтому отдельная база.

**П6. Платежи — максимальная изоляция**
`finance_payments` без FK наружу. Другие сервисы проверяют подписку через `subscription_grants` в `platform-core` (gateway), не через прямой доступ к базе платежей.

**П7. `SUBSCRIPTION_GRANTS` живёт в `platform-core` осознанно**
Gateway-паттерн: `payment-registry` синхронизирует гранты подписок в `platform-core` через `subscription-sync` cron. Другие сервисы читают авторизацию только из `platform-core` — единая точка проверки прав.

## Карта баз данных

```
Neon Project: aisystant
│
├── DB: platform-core      ← USER_IDENTITIES + SUBSCRIPTION_GRANTS
│                             + GITHUB_CONNECTIONS + GOOGLE_CALENDAR_CONNECTIONS
│                             + ORY_TOKENS + DT_TOKENS + BACKEND_REGISTRY
│                             + directus.* (схема Directus CMS ~15 таблиц)
│
├── DB: digital-twin       ← DIGITAL_TWINS + POINT_TRANSACTIONS
│                             + LEARNER_CONCEPT_MASTERY
│
├── DB: knowledge          ← DOCUMENTS + CONCEPTS + CONCEPT_EDGES
│                             + CONCEPT_MISCONCEPTIONS + RETRIEVAL_FEEDBACK
│                             + GITHUB_INSTALLATIONS + USER_SOURCES
│
├── DB: activity-hub       ← RAW_EVENTS (partitioned) + USER_EVENTS
│                             + LEARNING_HISTORY (Gold layer)
│                             + USER_INTEGRATIONS + IDENTITY_MAP
│                             + SYNC_LOG + QUARANTINED_EVENTS
│                             ⚠ кандидат на замену ClickHouse/TimescaleDB
│
├── DB: payment-registry   ← FINANCE_PAYMENTS + PAYMENTS_SYNC_STATE
│                             + SUBSCRIPTION_GRANTS_SYNC_STATE
│                             + IMPORT_STAGING_PAYMENT + IMPORT_STAGING_CHARGEOFF
│
├── DB: aist-bot           ← USER_STATE + QA_HISTORY + NOTIFICATION_LOG
│                             + BOT_SUBSCRIPTIONS + SEMINARS + SEMINAR_PAYMENTS
│                             + WORKSHOP_PAYMENTS + COMMUNITY_MEMBERS
│                             + SERVICE_USAGE + USER_ACCESS + FEEDBACK_TRIAGE
│
└── DB: metabase           ← служебные таблицы Metabase (~171 таблица)
                              dashboards, questions, users, collections…
                              читает payment-registry (read-only conn)
                              ⚠ НЕ хранит прикладные данные платформы

Вне Neon:
  Ory Kratos (отдельный сервис) ← идентичность, source of truth по ory_id
```

## Пояснение: USER_INTEGRATIONS vs GITHUB_CONNECTIONS

Обе таблицы хранят GitHub OAuth-токен одного и того же пользователя — это намеренный **dual-write**.

| | GITHUB_CONNECTIONS (platform-core) | USER_INTEGRATIONS (activity-hub) |
|---|---|---|
| **Кто пишет** | Бот (OAuth callback) | activity-hub (sync из github_connections) |
| **Назначение** | Конфигурация публикации: target_repo, notes_path, strategy_repo, branches | Server-side сбор данных: WakaTime sync, IWE Adapter |
| **Дополнительные поля** | knowledge_repo, strategy_repo, default_branch | scope, metadata (generic) |
| **Потребитель** | Бот-издатель (публикация заметок) | activity-hub collectors |

`GOOGLE_CALENDAR_CONNECTIONS` — только в `platform-core` (бот), дублирования нет.
`WAKATIME` — только в `USER_INTEGRATIONS` (activity-hub collector), в боте нет.

---

## Общая диаграмма: связи между базами

Показывает потоки данных и API-зависимости между базами. Все стрелки — API-вызовы или cron-синхронизация, не FK.

```mermaid
graph TD
    OryKratos([Ory Kratos\nexternal])

    subgraph Neon
        PC[(platform-core\nUSER_IDENTITIES\nSUBSCRIPTION_GRANTS\nOAuth connections)]
        DT[(digital-twin\nDIGITAL_TWINS\nPOINT_TRANSACTIONS\nLEARNER_MASTERY)]
        KN[(knowledge\nDOCUMENTS\nCONCEPTS\nCONCEPT_GRAPH)]
        AH[(activity-hub\nRAW_EVENTS\nUSER_EVENTS\nLEARNING_HISTORY)]
        PR[(payment-registry\nFINANCE_PAYMENTS\nSYNC_STATES)]
        AB[(aist-bot\nUSER_STATE\nQA_HISTORY\nBOT_SUBSCRIPTIONS)]
        MB[(metabase\n~171 служебных таблиц)]
    end

    OryKratos -->|"OAuth identity\n(source of truth)"| PC

    PC -->|"check subscription\n(API)"| AB
    PC -->|"check subscription\n(API)"| DT
    PC -->|"check subscription\n(API)"| KN
    PC -->|"user identity\n(API)"| AH

    PR -->|"subscription-sync cron\n→ SUBSCRIPTION_GRANTS"| PC

    AB -->|"write DT via\ndt-mcp API"| DT
    AB -->|"send events\n(collector)"| AH
    AB -->|"GitHub dual-write\n→ USER_INTEGRATIONS"| AH

    KN -->|"concept_id ref\n(API)"| DT

    AH -->|"transform pipeline\nRAW→USER_EVENTS"| AH
    AH -->|"learning events\n→ LEARNING_HISTORY\n(Gold layer)"| AH

    MB -->|"read finance data\n(read-only conn)"| PR
    MB -->|"read events\n(read-only conn)"| AH
```

---

## ERD по базам данных

> Связи между базами помечены `(API)` с указанием целевой базы.

---

### DB: platform-core

Ядро платформы: идентичность, подписки, OAuth-соединения, реестр персональных MCP.
**Gateway-паттерн:** все сервисы проверяют права только здесь, не идут напрямую в payment-registry.

```mermaid
erDiagram
    USER_IDENTITIES {
        uuid        ory_id          PK  "source: Ory Kratos"
        bigint      telegram_id     UK
        bigint      lms_id
        text        region          "ru|world"
        timestamptz created_at
    }

    SUBSCRIPTION_GRANTS {
        uuid        id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        bigint      telegram_id     "→ USER_IDENTITIES"
        text        product         "br = Бесконечное развитие"
        text        source          "payment|manual|marketing|trial"
        timestamptz valid_from
        timestamptz valid_until
        timestamptz revoked_at
        text        note            "синхронизируется из payment-registry cron"
    }

    GITHUB_CONNECTIONS {
        serial      id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        bigint      telegram_id
        text        github_username
        text        access_token
        text        refresh_token
        text        knowledge_repo
        text        strategy_repo
        text        default_branch
        timestamptz connected_at
        text        note            "dual-write: токен также в activity-hub.USER_INTEGRATIONS"
    }

    GOOGLE_CALENDAR_CONNECTIONS {
        serial      id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        bigint      telegram_id
        text        access_token
        text        refresh_token
        text        calendar_id
        text        email
        timestamptz connected_at
    }

    ORY_TOKENS {
        serial      id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        bigint      telegram_id
        text        access_token
        text        refresh_token
        timestamptz expires_at
    }

    DT_TOKENS {
        serial      id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        bigint      telegram_id
        text        token_value
        timestamptz expires_at
    }

    BACKEND_REGISTRY {
        serial      id              PK
        uuid        ory_id          "→ USER_IDENTITIES"
        text        backend_url
        text        tool_prefix
        text        name
        text        status          "pending_validation|active|failed"
        text        knowledge_gate_result "pending|passed|partial|failed"
        timestamptz created_at
    }

    USER_IDENTITIES ||--o{ SUBSCRIPTION_GRANTS : "has"
    USER_IDENTITIES ||--o{ GITHUB_CONNECTIONS : "connects GitHub"
    USER_IDENTITIES ||--o{ GOOGLE_CALENDAR_CONNECTIONS : "connects GCal"
    USER_IDENTITIES ||--o{ ORY_TOKENS : "SSO session"
    USER_IDENTITIES ||--o{ DT_TOKENS : "DT access"
    USER_IDENTITIES ||--o{ BACKEND_REGISTRY : "personal MCPs"
```

---

### DB: digital-twin

Цифровой двойник пользователя. Writers: dt-mcp, profiler cron, бот (только через DT-MCP API).

```mermaid
erDiagram
    DIGITAL_TWINS {
        uuid        id              PK
        uuid        ory_id          UK  "ref→platform-core.USER_IDENTITIES (API)"
        jsonb       layer_1         "Базовые параметры"
        jsonb       layer_2         "Вовлечённость"
        jsonb       layer_3         "Производные: agency, longevity…"
        timestamptz updated_at
    }

    POINT_TRANSACTIONS {
        bigserial   id              PK
        uuid        ory_id          "ref→platform-core.USER_IDENTITIES (API)"
        text        rule_code
        int         points_delta
        text        source          "bot|lms|hub|manual"
        timestamptz created_at
    }

    LEARNER_CONCEPT_MASTERY {
        bigserial   id              PK
        uuid        ory_id          "ref→platform-core.USER_IDENTITIES (API)"
        bigint      concept_id      "ref→knowledge.CONCEPTS (API)"
        float       mastery         "0.0–1.0 Bayesian"
        int         attempts
        timestamptz last_assessed_at
    }

    DIGITAL_TWINS ||--o{ POINT_TRANSACTIONS : "accumulates points"
    DIGITAL_TWINS ||--o{ LEARNER_CONCEPT_MASTERY : "qualification progress"
```

---

### DB: knowledge

Документы платформы и пользователей, граф концептов, источники для индексации.

```mermaid
erDiagram
    DOCUMENTS {
        bigserial   id              PK
        text        filename
        text        source
        text        source_type     "platform|personal|github"
        vector      embedding       "1024-dim HNSW"
        tsvector    search_vector
        uuid        user_id         "NULL=platform, set=personal (API→platform-core)"
        bigint      parent_id       FK
        timestamptz created_at
    }

    RETRIEVAL_FEEDBACK {
        bigserial   id              PK
        bigint      document_id     FK
        text        query_hash
        bool        helpfulness
        timestamptz created_at
    }

    CONCEPTS {
        bigserial   id              PK
        text        code            UK  "ZP.001, FPF.MIM.001…"
        text        name
        text        level           "zp|fpf|pack|guide|course"
        text        domain          "MIM|DP|PD|ECO"
        bigint      document_id     FK
        vector      embedding
        text        status          "active|deprecated|draft"
    }

    CONCEPT_EDGES {
        bigserial   id              PK
        bigint      from_concept_id FK
        bigint      to_concept_id   FK
        text        edge_type       "prerequisite|related|part_of|contradicts"
        float       weight
    }

    CONCEPT_MISCONCEPTIONS {
        bigserial   id              PK
        bigint      concept_id      FK
        text        category        "wrong_concept|folk_substitution"
        text        misconception_text
        text        correct_version
    }

    GITHUB_INSTALLATIONS {
        serial      id              PK
        uuid        user_id         "ref→platform-core.USER_IDENTITIES (API)"
        bigint      github_user_id
        text        installation_id UK
        jsonb       repos
        timestamptz created_at
    }

    USER_SOURCES {
        serial      id              PK
        uuid        user_id         "ref→platform-core.USER_IDENTITIES (API)"
        text        source
        text        github_repo
        bool        active
    }

    DOCUMENTS ||--o{ DOCUMENTS : "parent→chunks"
    DOCUMENTS ||--o{ RETRIEVAL_FEEDBACK : "feedback"
    DOCUMENTS ||--o{ CONCEPTS : "sourced from"
    CONCEPTS ||--o{ CONCEPT_EDGES : "from"
    CONCEPTS ||--o{ CONCEPT_EDGES : "to"
    CONCEPTS ||--o{ CONCEPT_MISCONCEPTIONS : "has"
    GITHUB_INSTALLATIONS ||--o{ USER_SOURCES : "enables sources"
```

---

### DB: activity-hub

Medallion-архитектура: Landing (raw) → Silver (user_events) → Gold (learning_history).
Кандидат на замену ClickHouse/TimescaleDB при росте объёма событий.

```mermaid
erDiagram
    RAW_EVENTS {
        bigserial   id              PK
        text        source          "lms|bot|club|iwe"
        text        external_id
        jsonb       payload         "сырой, без трансформаций"
        timestamptz fetched_at
        text        transform_status "pending|done|failed|skipped"
    }

    USER_EVENTS {
        bigserial   id              PK
        text        source
        text        external_id     UK
        text        event_type
        uuid        user_uuid       "ref→platform-core.USER_IDENTITIES (API)"
        jsonb       payload
        timestamptz occurred_at
    }

    LEARNING_HISTORY {
        bigserial   id              PK
        uuid        user_uuid       "ref→platform-core.USER_IDENTITIES (API)"
        bigint      event_id        FK
        text        element_id
        text        element_type    "meme|practice|cell"
        int         area            "1-5"
        float       score
        bool        passed
        int         schema_version  "1=legacy, 2=current"
        timestamptz created_at
        text        note            "Gold layer: материализация learning_* событий из USER_EVENTS"
    }

    IDENTITY_MAP {
        bigserial   id              PK
        text        source
        text        external_id
        uuid        user_uuid       "ref→platform-core.USER_IDENTITIES (API)"
    }

    QUARANTINED_EVENTS {
        bigserial   id              PK
        text        source
        text        reason
        jsonb       payload
        timestamptz created_at
    }

    SYNC_LOG {
        bigserial   id              PK
        text        source
        text        status          "success|partial|failed"
        int         events_fetched
        int         events_written
        int         events_quarantined
        timestamptz started_at
        timestamptz finished_at
    }

    USER_INTEGRATIONS {
        serial      id              PK
        uuid        user_uuid       "ref→platform-core.USER_IDENTITIES (API)"
        text        service         "github|wakatime|google_calendar"
        text        access_token
        text        refresh_token
        timestamptz token_expires_at
        text        scope
        jsonb       metadata
        timestamptz connected_at
        text        note            "GitHub: dual-write из platform-core.GITHUB_CONNECTIONS"
    }

    RAW_EVENTS ||--o{ USER_EVENTS : "transform → silver"
    USER_EVENTS ||--o{ LEARNING_HISTORY : "materialize → gold"
    RAW_EVENTS ||--o{ QUARANTINED_EVENTS : "invalid → quarantine"
    USER_INTEGRATIONS }o--|| IDENTITY_MAP : "resolves user"
```

---

### DB: payment-registry

Реестр всех платежей. Максимальная изоляция: другие сервисы не имеют прямого доступа к базе.
Синхронизирует `SUBSCRIPTION_GRANTS` в `platform-core` через cron `subscription-sync`.

```mermaid
erDiagram
    FINANCE_PAYMENTS {
        bigserial   id              PK
        text        ory_id          "ref→platform-core.USER_IDENTITIES (API)"
        bigint      telegram_id     "ref→platform-core.USER_IDENTITIES (API)"
        bigint      suser_id        "Aisystant legacy ID"
        text        purpose         "BALANCE|SUBSCRIPTION|DONATION|INTERNSHIP|WORKSHOP"
        decimal     amount
        text        currency        "RUB|USD|EUR|STARS"
        int         channel         "1=YooKassa 2=Paybox 3=Stripe 8=Manual"
        text        status
        timestamptz charged_off_at
        timestamptz archived_at
    }

    PAYMENTS_SYNC_STATE {
        int         id              PK  "singleton id=1"
        bigint      last_payment_id
        timestamptz last_sync_at
        text        last_sync_status
    }

    SUBSCRIPTION_GRANTS_SYNC_STATE {
        int         id              PK  "singleton id=1"
        bigint      last_subscription_id
        timestamptz last_sync_at
        text        note            "tracks sync boundary → platform-core.SUBSCRIPTION_GRANTS"
    }

    IMPORT_STAGING_PAYMENT {
        bigint      id
        text        ext_id
        bigint      suser_id
        text        purpose
        text        payment_system
        decimal     amount
        bool        success
        text        note            "⚙ landing zone для incremental sync из Aisystant"
    }

    IMPORT_STAGING_CHARGEOFF {
        bigint      id
        bigint      suser_id
        decimal     amount
        bigint      payment_id      "→ FINANCE_PAYMENTS"
        text        note            "⚙ landing zone для incremental sync"
    }

    IMPORT_STAGING_PAYMENT ||--o{ FINANCE_PAYMENTS : "syncs into"
    IMPORT_STAGING_CHARGEOFF ||--o{ FINANCE_PAYMENTS : "links to"
```

---

### DB: aist-bot

Только бот. Telegram-first: основной ключ — `chat_id`. Не содержит платформенных данных.

```mermaid
erDiagram
    USER_STATE {
        bigint      chat_id         PK  "Telegram chat_id"
        text        current_state   "FSM state"
        jsonb       data            "FSM context"
        timestamptz trial_started_at
        timestamptz last_active_date
        int         active_days_total
    }

    QA_HISTORY {
        bigserial   id              PK
        bigint      chat_id         FK
        text        question
        text        answer
        bool        helpful
        jsonb       mcp_sources
        timestamptz created_at
    }

    NOTIFICATION_LOG {
        bigserial   id              PK
        bigint      chat_id
        text        notification_type
        text        idempotency_key UK
        jsonb       payload
        timestamptz created_at
    }

    BOT_SUBSCRIPTIONS {
        bigserial   id              PK
        bigint      chat_id
        text        status          "active|inactive"
        text        telegram_payment_charge_id
        int         stars_amount
        timestamptz started_at
        timestamptz expires_at
    }

    SEMINARS {
        serial      id              PK
        text        title
        decimal     price_rub
        int         price_stars
        bigint      tg_group_id     "Telegram group"
        date        event_date
    }

    SEMINAR_PAYMENTS {
        bigserial   id              PK
        bigint      telegram_id
        int         seminar_id      FK
        decimal     amount
        text        currency        "RUB|STARS"
        text        status
        timestamptz paid_at
    }

    WORKSHOP_PAYMENTS {
        bigserial   id              PK
        bigint      telegram_id
        text        aisystant_id    "ref→Aisystant LMS user (external, API)"
        decimal     amount
        text        status          "pending|success"
        text        source          "bot|web"
        timestamptz paid_at
    }

    COMMUNITY_MEMBERS {
        bigserial   id              PK
        bigint      telegram_id
        bigint      chat_id
        text        username
        timestamptz joined_at
        timestamptz left_at
    }

    SERVICE_USAGE {
        bigserial   id              PK
        bigint      user_id         "Telegram ID"
        text        service_id
        timestamptz created_at
    }

    USER_ACCESS {
        bigserial   id              PK
        bigint      user_id         "Telegram ID"
        text        access_type
        text        resource_id
        timestamptz expires_at
    }

    FEEDBACK_TRIAGE {
        bigserial   id              PK
        bigint      chat_id
        text        category
        text        status          "new|classified|resolved"
        text        source          "bot|club|manual"
        timestamptz created_at
    }

    USER_STATE ||--o{ QA_HISTORY : "conversation"
    USER_STATE ||--o{ NOTIFICATION_LOG : "notifications"
    USER_STATE ||--o{ BOT_SUBSCRIPTIONS : "TG Stars billing"
    SEMINARS ||--o{ SEMINAR_PAYMENTS : "paid by"
```

---

### DB: metabase

Служебные таблицы Metabase BI (~171 таблица). Не хранит прикладные данные платформы.
Читает `payment-registry` и `activity-hub` через отдельные read-only connections.

```mermaid
erDiagram
    METABASE_COLLECTIONS {
        int     id          PK
        text    name
        text    color
        int     parent_id   FK
    }

    METABASE_DASHBOARDS {
        int     id              PK
        text    name
        text    description
        int     collection_id   FK
    }

    METABASE_CARDS {
        int     id              PK
        text    name
        text    display
        int     dashboard_id    FK
        text    dataset_query   "SQL / MBQL"
    }

    METABASE_USERS {
        int     id          PK
        text    email       UK
        text    first_name
        text    last_name
        bool    is_superuser
        bool    is_active
    }

    METABASE_COLLECTIONS ||--o{ METABASE_COLLECTIONS : "parent→child"
    METABASE_COLLECTIONS ||--o{ METABASE_DASHBOARDS : "contains"
    METABASE_DASHBOARDS ||--o{ METABASE_CARDS : "contains"
```

---

## Кто читает / кто пишет

| База | Writers | Readers |
|------|---------|---------|
| platform-core | Ory callback, OAuth flows, `subscription-sync` cron (из payment-registry) | gateway-mcp (авторизация каждого запроса), все сервисы через API |
| digital-twin | dt-mcp, profiler cron, бот (через DT-MCP API) | dt-mcp, бот `/twin`, knowledge-mcp (рекомендации) |
| knowledge | knowledge-mcp ingest, GitHub webhook | knowledge-mcp search, dt-mcp |
| activity-hub | collectors (lms/bot/club/iwe), transform-worker, бот (dual-write GitHub токена) | transform-worker, Metabase (RO) |
| payment-registry | `incremental-sync.sh` cron, Directus API | Metabase (RO), `subscription-sync` cron |
| aist-bot | только бот | только бот |
| metabase | Metabase internal | Metabase UI |

## Статус

> **Целевая архитектура.** Текущее состояние (14 апр 2026): все таблицы в одной базе `platform` (WP-232).
> Разделение на 7 баз — следующий РП (~20-40h).
> Решение принято: встреча ИТ 8, 14 апр 2026.
