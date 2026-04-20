---
id: DP.ARCH.004
name: Архитектура данных Neon (Database-per-Service)
type: domain-entity
status: active
valid_from: 2026-04-14
summary: "9 баз данных в Neon по принципу database-per-service. ERD, диаграммы связей, справочник таблиц, принятые архитектурные решения. Принята на встрече ИТ 14 апр 2026, расширена 19 апр 2026 (content-pipeline)."
related:
  specializes: [U.System]
  uses: [DP.SOTA.016, DP.ARCH.003, DP.ARCH.001]
decision_source: "Встреча ИТ 8, 14 апр 2026 (транскрипция Zoom)"
supersedes: "WP-232 решение об одной базе platform"
---

# DP.ARCH.004 — Архитектура данных Neon

Архитектура данных платформы: 9 баз Neon, какие физ.объекты живут в каждой, как базы связаны между собой, кто пишет и читает. Ниже — три ключевые диаграммы: **карта 9 БД** (в каждой базе — объекты физ.мира: человек, курс, платёж, событие), **ERD по каждой БД** (концептуальные ER-диаграммы сущностей и связей) и **обзорная** (все системы + 9 баз + агенты в одном виде). Контекст, принципы, реестр таблиц и принятые решения — в разделах после диаграмм.

---

<details>
<summary><b>1. Карта 9 БД</b></summary>

В каждом кластере — все физ.объекты этой БД по реестру §7.0.2 (узел = объект физ.мира, которому можно дать имя собственное). Сплошная линия внутри кластера — связь двух объектов. Пунктирная с подписью — связь 🔗 с атрибутами (наставничество, OAuth): это ребро, а не узел. Стрелка `-->` — поток трансформации (Bronze→Silver→Gold в #4, Job→Post в #9). Стрелки между кластерами `-.->` — межбазовые связи и read-only аналитика. Технические таблицы (`*_log`, `*_session`, `*_cache`, `*_state`, `*_snapshot`) не показаны — они в §10. Правила построения — `DP.METHOD.040`.

```mermaid
flowchart LR
    classDef db_gw    fill:#dbeafe,stroke:#3b82f6,color:#1e3a5f
    classDef db_prod  fill:#dcfce7,stroke:#22c55e,color:#14532d
    classDef db_infra fill:#f3f4f6,stroke:#9ca3af,color:#374151

    subgraph DB1 ["#1 platform-core"]
      direction TB
      U1[Созидатель]
      P1["Продукт-экземпляр<br/>(курс СМ-2026-S2)"]
      S1["Подписка-контракт<br/>(01.04–01.07)"]
      B1[Персональный<br/>MCP-бэкенд]
      Q1[Присвоение<br/>квалификации]
      QL1["Уровень квалификации<br/>(справочник, 11 ступеней)"]
      PR1[Правило начисления<br/>баллов]
      PT1[Транзакция баллов]
      PM1[Множитель<br/>квалификации]
      U1 --- S1
      P1 --- S1
      U1 --- B1
      U1 --- Q1
      Q1 --- QL1
      U1 --- PT1
      PT1 --- PR1
      PT1 --- PM1
      U1 -. "наставничество<br/>(Созидатель ↔ Продукт)" .- P1
    end
    class DB1 db_gw

    subgraph DB2 ["#2 digital-twin"]
      direction TB
      DT2[Цифровой двойник]
      Ind2[Показатель развития]
      St2[Стадия развития]
      DT2 --- Ind2
      DT2 --- St2
    end
    class DB2 db_prod

    subgraph DB3 ["#3 knowledge"]
      direction TB
      D3[Документ]
      C3[Концепт]
      Mis3[Концептное заблуждение]
      Src3[Источник индексации]
      Src3 --- D3
      D3 --- C3
      C3 --- Mis3
    end
    class DB3 db_prod

    subgraph DB4 ["#4 activity-hub"]
      direction TB
      Bz4["Сырое событие<br/>(Bronze)"]
      Sv4["Событие созидателя<br/>(Silver)"]
      Gd4["Факт обучения<br/>(Gold)"]
      Bz4 --> Sv4
      Sv4 --> Gd4
    end
    class DB4 db_prod

    subgraph DB5 ["#5 payment-registry"]
      direction TB
      Pay5[Платёж]
      Auto5["Автосписание-договор<br/>(payment_credentials)"]
      Auto5 --- Pay5
    end
    class DB5 db_prod

    subgraph DB6 ["#6 aist-bot"]
      direction TB
      UB6[Пользователь бота]
      Ans6[Ответ на задание]
      Asm6[Оценка]
      Tr6[Попытка тренажёра]
      Mar6[Элемент марафона]
      QA6[QA-пара]
      CM6[Участник сообщества]
      Ch6[Мониторируемый<br/>TG-канал]
      FB6[Feedback-запись]
      Fd6[Недельный цикл ленты]
      UB6 --- Ans6
      Ans6 --- Asm6
      UB6 --- Tr6
      Mar6 --- Tr6
      UB6 --- QA6
      UB6 --- CM6
      CM6 --- Ch6
      UB6 --- FB6
      UB6 --- Fd6
    end
    class DB6 db_prod

    subgraph DB7 ["#7 metabase"]
      direction TB
      Col7[Коллекция дашбордов]
      Dash7[Дашборд]
      Card7[Карта-запрос]
      An7[Аналитик]
      Col7 --- Dash7
      Dash7 --- Card7
      An7 --- Dash7
    end
    class DB7 db_infra

    subgraph DB8 ["#8 health"]
      direction TB
      Srv8[Микросервис-деплоймент]
      Inc8[Инцидент]
      Fix8[Задание auto-fix]
      Srv8 --- Inc8
      Inc8 --- Fix8
    end
    class DB8 db_infra

    subgraph DB9 ["#9 content-pipeline"]
      direction TB
      Job9[Задание-публикация]
      Post9[Публикация]
      Ch9[Канал соцсети]
      As9[Ассет]
      Sch9[Расписание публикации]
      Job9 --> Post9
      Job9 --- Sch9
      Job9 --- As9
      Post9 --- Ch9
    end
    class DB9 db_prod

    %% ── Связи между БД (по объектам физ.мира, нумерация #1..#9) ──
    DB1 -.->|"владелец двойника"| DB2
    DB1 -.->|"владелец события"| DB4
    DB1 -.->|"получатель платежа"| DB5
    DB1 -.->|"участник сессии"| DB6
    DB1 -.->|"автор публикаций"| DB9
    DB3 -.->|"освоение концептов"| DB2
    DB4 -.->|"сигналы развития"| DB2
    DB5 -.->|"активация подписки"| DB1
    DB9 -.->|"событие публикации"| DB4
    DB8 -.->|"статусы сервисов"| DB7
    DB4 -.->|"события (без PII)"| DB7
    DB5 -.->|"финансы (RLS)"| DB7
```

</details>

---

<details open>
<summary><b>2. ERD по базам данных</b></summary>

> Концептуальная ER: только объекты физ.мира (`DP.METHOD.040` §1, HD «ER ≠ Физ.схема», «Объект ≠ Отношение»). Реестр физ.объектов — §7.0.2. Физ.схема с колонками и FK — §10. Связи между БД — §7.5. Не показаны технические таблицы (`*_log`, `*_session`, `*_cache`, `*_snapshot`, `*_state`, `*_staging`), промежуточные M:N-связки без атрибутов и копии/проекции — о них примечание после каждой ER.

### DB #1: platform-core

Ядро платформы: созидатели, продукты, подписки, квалификации, баллы. Источник истины идентичности и прав.

**BC:** Identity & Access + Subscription + Qualification + Points Economy.

```mermaid
erDiagram
    СОЗИДАТЕЛЬ ||--o{ ПОДПИСКА_КОНТРАКТ : "держит"
    СОЗИДАТЕЛЬ ||--o{ ПЕРСОНАЛЬНЫЙ_MCP_БЭКЕНД : "регистрирует"
    СОЗИДАТЕЛЬ ||--o{ ПРИСВОЕНИЕ_КВАЛИФИКАЦИИ : "получает"
    СОЗИДАТЕЛЬ ||--o{ ТРАНЗАКЦИЯ_БАЛЛОВ : "зарабатывает"
    СОЗИДАТЕЛЬ }o--o{ ПРОДУКТ_ЭКЗЕМПЛЯР : "наставничество"
    СОЗИДАТЕЛЬ }o--o{ GITHUB_АККАУНТ : "публикация (scope=repo)"
    СОЗИДАТЕЛЬ }o--o{ GOOGLE_КАЛЕНДАРЬ : "OAuth-связка"
    СОЗИДАТЕЛЬ }o--o{ WAKATIME_АККАУНТ : "метрики кода"
    ПРОДУКТ_ЭКЗЕМПЛЯР ||--o{ ПОДПИСКА_КОНТРАКТ : "предоставляется по"
    УРОВЕНЬ_КВАЛИФИКАЦИИ ||--o{ ПРИСВОЕНИЕ_КВАЛИФИКАЦИИ : "присваивается"
    УРОВЕНЬ_КВАЛИФИКАЦИИ ||--o| МНОЖИТЕЛЬ_КВАЛИФИКАЦИИ : "умножает балл на"
    ПРАВИЛО_НАЧИСЛЕНИЯ_БАЛЛОВ ||--o{ ТРАНЗАКЦИЯ_БАЛЛОВ : "порождает"

    СОЗИДАТЕЛЬ {
        string имя "Иванов Иван"
        string регион "ru | world"
        string tier "T0 — T4"
    }
    ПРОДУКТ_ЭКЗЕМПЛЯР {
        string код "СМ-2026-S2, OD-семинар-19.04"
        string тип "subscription | program | seminar"
        string название
        date event_date "для семинара"
    }
    ПОДПИСКА_КОНТРАКТ {
        date действует_с
        date действует_до
        string источник "payment | manual | marketing | trial"
    }
    ПЕРСОНАЛЬНЫЙ_MCP_БЭКЕНД {
        string url "BYOB, validated WP-239"
        string префикс_инструментов
    }
    УРОВЕНЬ_КВАЛИФИКАЦИИ {
        string код "Интересант — Общественный деятель (11 ступеней)"
        int порядок
    }
    ПРИСВОЕНИЕ_КВАЛИФИКАЦИИ {
        date присвоено
        string обоснование
        string источник "LMS | Методсовет | auto"
    }
    МНОЖИТЕЛЬ_КВАЛИФИКАЦИИ {
        decimal множитель "×1.0 — ×5.0"
        int daily_cap "100 — 1000"
    }
    ПРАВИЛО_НАЧИСЛЕНИЯ_БАЛЛОВ {
        string событие
        int базовый_балл "2 — 69"
        string категория "×1 | ×2 | ×3 | ×5"
    }
    ТРАНЗАКЦИЯ_БАЛЛОВ {
        int величина
        timestamp момент
        string event_id UK "идемпотентность"
    }
    GITHUB_АККАУНТ {
        string username "внешний объект"
    }
    GOOGLE_КАЛЕНДАРЬ {
        string calendar_id "внешний объект"
    }
    WAKATIME_АККАУНТ {
        string api_user "внешний объект"
    }
```

**Примечания.**
- ⚠️ `TIER_EVENTS` (событие смены тира) концептуально принадлежит #4 activity-hub — не показан на этой ER (PMB-кандидат).
- ⚠️ `Баланс баллов` = агрегат-кеш транзакций, не физ.объект — на ER не показан.
- 🔗 «Наставничество» = связь Созидатель(наставник)↔Продукт с атрибутом роли (lead/assistant/reviewer); на физ.схеме — `MENTOR_ASSIGNMENTS`.
- 🔗 Три внешних OAuth-связки (GitHub/GCal/WakaTime) — связи с внешними физ.объектами, не узлы домена. Показаны прямоугольниками с пометкой «внешний объект» для читаемости Mermaid.

---

### DB #2: digital-twin

Модель созидателя — снимок в трёх слоях (базовые параметры / вовлечённость / производные индикаторы).

**BC:** Digital Twin.

```mermaid
erDiagram
    СОЗИДАТЕЛЬ ||--|| ЦИФРОВОЙ_ДВОЙНИК : "имеет (1:1)"
    ЦИФРОВОЙ_ДВОЙНИК ||--|{ ПОКАЗАТЕЛЬ_РАЗВИТИЯ : "содержит (слабая)"
    ЦИФРОВОЙ_ДВОЙНИК ||--|| СТАДИЯ_РАЗВИТИЯ : "находится на"
    СОЗИДАТЕЛЬ }o--o{ КОНЦЕПТ : "освоение (p=0..1)"

    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
    ЦИФРОВОЙ_ДВОЙНИК {
        jsonb layer_1 "базовые параметры"
        jsonb layer_2 "вовлечённость"
        jsonb layer_3 "производные: agency, longevity"
        timestamp обновлено
    }
    ПОКАЗАТЕЛЬ_РАЗВИТИЯ {
        string код "IND.3.*"
        decimal значение
        timestamp измерено
    }
    СТАДИЯ_РАЗВИТИЯ {
        string код "Random | Practicing | Systematic | Disciplined | Proactive"
        date с_даты
        string обоснование
    }
    КОНЦЕПТ {
        bigint concept_id "внешний — из #3"
    }
```

**Примечания.**
- 🔗 «Освоение концепта» = связь Созидатель↔Концепт с атрибутами (mastery 0.0-1.0, confidence, attempts, last_assessed_at); на физ.схеме — `LEARNER_CONCEPT_MASTERY`. Пишет knowledge-mcp (через API →#3).
- **Показатель развития** — слабая сущность: живёт только в контексте двойника, хранится в колонках JSONB. PK составной (twin_id + код).
- **Стадия развития** — слабая сущность в контексте двойника (хранится колонкой `stage` в DIGITAL_TWINS); показана отдельно по §7.0.2 (✅ физ.объект — конкретная позиция созидателя на шкале зрелости).
- ⚠️ `profiling_runs` (запуск профайлера, WP-218 план) = технический лог, не физ.объект; PMB-7.
- ⚠️ `POINT_TRANSACTIONS` перенесён в #1 (`platform.points.*`), на ER #2 не показан.

---

### DB #3: knowledge

Документы и концепты платформы и созидателей. Граф понятий, источники для индексации.

**BC:** Knowledge Base.

```mermaid
erDiagram
    ДОКУМЕНТ }o--|| КОНЦЕПТ : "содержит | определяет"
    КОНЦЕПТ }o--o{ КОНЦЕПТ : "граф (prerequisite | related | part_of | contradicts)"
    КОНЦЕПТ ||--o{ КОНЦЕПТНОЕ_ЗАБЛУЖДЕНИЕ : "имеет"
    СОЗИДАТЕЛЬ ||--o{ ИСТОЧНИК_ИНДЕКСАЦИИ : "подключает"
    СОЗИДАТЕЛЬ }o--o{ GITHUB_АККАУНТ : "индексация (scope=repo read)"

    ДОКУМЕНТ {
        string путь "напр. DP.ARCH.004-neon-data-architecture.md"
        string тип_источника "platform | personal | github"
        vector embedding "1024-dim"
    }
    КОНЦЕПТ {
        string код "ZP.001, FPF.MIM.001, DP.D.050"
        string название
        string уровень "zp | fpf | pack | guide | course"
        string домен "MIM | DP | PD | ECO"
    }
    КОНЦЕПТНОЕ_ЗАБЛУЖДЕНИЕ {
        string категория "wrong_concept | folk_substitution"
        text текст_заблуждения
        text корректная_версия
    }
    ИСТОЧНИК_ИНДЕКСАЦИИ {
        string url "репо или внешний ресурс"
        bool активен
    }
    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
    GITHUB_АККАУНТ {
        bigint github_user_id "внешний объект"
    }
```

**Примечания.**
- **Документ** — физ.объект (конкретный .md-файл с путём, эмбеддингом). Чанки/родительская иерархия — техническая декомпозиция, на ER не отдельной сущностью.
- **Концепт↔Концепт** = связь с атрибутом `edge_type`, на физ.схеме — `CONCEPT_EDGES`.
- 🔗 «Индексация» = связь Созидатель↔GitHub-аккаунт scope=repo(read), с атрибутом installation_id; на физ.схеме — `GITHUB_INSTALLATIONS`.
- ⚠️ `RETRIEVAL_FEEDBACK` = событие (факт оценки релевантности), не объект; не показан.

---

### DB #4: activity-hub

Поток событий созидателей из всех внешних систем. Medallion-архитектура: Bronze → Silver → Gold.

**BC:** Activity & Events. **Замечание:** на концептуальном уровне «событие» — физ.объект (факт, который произошёл с атрибуцией). Три слоя — не три разные сущности, а три стадии жизни одного события. Показано как один объект «Событие созидателя» + атрибут `слой`. Отдельно показано «Сырое событие» (до атрибуции — нет связи с Созидателем) и «Факт обучения» (агрегированный для профайлера).

```mermaid
erDiagram
    ВНЕШНЯЯ_СИСТЕМА ||--o{ СЫРОЕ_СОБЫТИЕ : "генерирует"
    СЫРОЕ_СОБЫТИЕ ||--o| СОБЫТИЕ_СОЗИДАТЕЛЯ : "нормализуется в"
    СОЗИДАТЕЛЬ ||--o{ СОБЫТИЕ_СОЗИДАТЕЛЯ : "совершает"
    СОБЫТИЕ_СОЗИДАТЕЛЯ ||--o| ФАКТ_ОБУЧЕНИЯ : "агрегируется в"

    ВНЕШНЯЯ_СИСТЕМА {
        string source "lms | club | wakatime | iwe | bot"
    }
    СЫРОЕ_СОБЫТИЕ {
        string external_id "ID в источнике"
        jsonb payload "без трансформаций"
        timestamp fetched_at
    }
    СОБЫТИЕ_СОЗИДАТЕЛЯ {
        string event_type
        jsonb payload "нормализованный"
        timestamp occurred_at
        string trace_id "OTel"
    }
    ФАКТ_ОБУЧЕНИЯ {
        string element_id
        string element_type "meme | practice | cell"
        int area "1 — 5"
        float score
        bool passed
    }
    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
```

**Примечания.**
- 🔗 `IDENTITY_MAP` = связь «внешний идентификатор (chat_id / lms_id) ↔ Созидатель», слабая идентификация до OAuth. На ER не узел — показана как атрибут связи `Сырое событие → Событие созидателя` (нормализация через identity resolver).
- ⚠️ `QUARANTINED_EVENTS` = технический sink (события, не прошедшие валидацию) — не на ER.
- ⚠️ `SYNC_LOG` = технический лог запусков коллекторов — не на ER.
- ⚠️ `TIER_EVENTS` (из #1) концептуально сюда — PMB.
- ✅ `CONVERSION_EVENTS` — принято 20 апр: перенос из #5/бота в #4 activity-hub (событие воронки, без payment_id/amount).

---

### DB #5: payment-registry

Финансовые транзакции и автосписания. Единственная БД платформы с платёжной информацией.

**BC:** Payments.

```mermaid
erDiagram
    СОЗИДАТЕЛЬ ||--o{ ПЛАТЁЖ : "совершает"
    СОЗИДАТЕЛЬ ||--o| АВТОСПИСАНИЕ_ДОГОВОР : "заключает"
    ПРОДУКТ_ЭКЗЕМПЛЯР ||--o{ ПЛАТЁЖ : "оплачивается за"
    АВТОСПИСАНИЕ_ДОГОВОР ||--o{ ПЛАТЁЖ : "порождает (расписанием)"
    ПЛАТЁЖ ||--o| ПОДПИСКА_КОНТРАКТ : "активирует (в #1)"

    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
    ПРОДУКТ_ЭКЗЕМПЛЯР {
        string код "внешний — из #1"
    }
    ПОДПИСКА_КОНТРАКТ {
        uuid id "внешний — из #1"
    }
    ПЛАТЁЖ {
        string provider_id "yookassa|stripe|paybox|tg_stars payment ID"
        decimal amount
        string currency "RUB | USD | EUR | STARS"
        string purpose "BALANCE | SUBSCRIPTION | DONATION | WORKSHOP | SEMINAR"
        timestamp charged_off_at
        string status
    }
    АВТОСПИСАНИЕ_ДОГОВОР {
        string payment_method_id "YooKassa, класс payment_credentials"
        string card_last4
        string cron_schedule "10:00 и 21:00 МСК"
        bool активен
    }
```

**Примечания.**
- **Платёж** — физ.объект (конкретная транзакция с ID провайдера, суммой, датой). Физ.реализация в трёх таблицах — `FINANCE_PAYMENTS` (основная), `SEMINAR_PAYMENTS`, `WORKSHOP_PAYMENTS` (кандидаты на миграцию из #6).
- **Автосписание-договор** — контракт-объект между созидателем и платформой на регулярное списание с сохранённой карты. Класс данных — `payment_credentials` (§5 П6.1), строже PII.
- 🔗 Активация подписки — факт: успешный платёж с `purpose=SUBSCRIPTION` создаёт `SUBSCRIPTION_GRANT` в #1 через `sync-subscriptions.sh`. На ER показано связью «активирует» с внешней сущностью.
- ⚠️ `PROCESSED_WEBHOOKS` = технический idempotency-механизм, не узел ER.
- ⚠️ `FINANCE_PAYMENTS_AUDIT_LOG` = лог изменений статуса (WP-237), на ER не узел (только на физ.схеме §10).
- ⚠️ `IMPORT_STAGING_*`, `*_SYNC_STATE` = landing zone и watermark — технические.
- ✅ `CONVERSION_EVENTS` — принято 20 апр: событие воронки → #4 activity-hub (не #5).

#### Autopay subsystem (подсистема автосписаний)

**Что это.** Автоматическое списание с сохранённой карты пользователя при истечении подписки, без его участия в момент списания. Используется YooKassa-механизм `save_payment_method=true` + повторный POST `/v3/payments` с сохранённым `payment_method_id`.

**Текущее состояние (промежуточное, owner — LMS):**

1. **Источники данных:** LMS таблица `subscription` (колонка `autopay boolean`) — флаг автопродления. Таблица `payment` (колонка `autopay_data varchar(1023)`) — JSON `{"type":"bank_card","payment_method_id":"...","card.last4":"1234"}`, сохранённый при первом платеже.
2. **Триггер:** `PaymentSchedulerService.autopayCheck10/21` — Spring `@Scheduled` cron 10:00 и 21:00 МСК.
3. **Отбор:** `subscriptionRepository.findAllByToDateAndAutopay(today, true)` — подписки, истекающие сегодня.
4. **Списание:** `PaymentUtil.createAutoPaymentForSubscription` → POST `/v3/payments` с `payment_method_id` + `capture:true` + `save_payment_method:true`. Merchant — один на всю систему (`Keys.getYoAuth()`, ИП).
5. **Подтверждение:** webhook `/yoo/hook` + polling `updateYooPayments` каждые 30 мин. При `succeeded` → `processPayment` продлевает подписку.
6. **Нотификации:** `paymentNotificationsCheck` шлёт письма за 7/3/1 день до списания + `sendPaymentAutoExtendMessage` после успеха. При провале письма нет.
7. **Отмена:** `POST /disable-autopay` ставит `subscription.autopay=false`. YooKassa unlink API **не вызывается** — токен остаётся действительным на стороне YooKassa.
8. **Зеркало в `payment-registry`:** `incremental-sync.sh` каждые 10 мин копирует `payment.autopay` и `payment.autopay_data` в `finance_payments` (пока read-only, не используется на чтение).

**Целевое состояние (после миграции, owner — мы):**

Подсистема **портируется**, не проектируется заново. Механизм рабочий; меняется только место исполнения:

| LMS (текущее) | Наш стек (целевое) |
|---|---|
| `PaymentSchedulerService.@Scheduled` | CF Worker `payment-scheduler` (Cloudflare Cron Triggers) |
| LMS Postgres `subscription` + `payment` | Neon `subscription_grants` + `finance_payments.autopay_data` |
| `Keys.getYoAuth()` в LMS env | CF Secrets — **те же ключи** (merchant не меняется, токены валидны) |
| `YooWebHook → updateYooPayments` | `payment-receiver` CF Worker (Ф1 WP-246 DONE) |
| `MailService.sendPayment*Message` | Email-провайдер бота |
| `POST /disable-autopay` (без unlink) | Новый endpoint + `DELETE /v3/payment_methods/{id}` (добавляем unlink) |

**Критичное операционное условие:** merchant-аккаунт YooKassa у LMS и у нас — один и тот же (ИП). Сохранённые `payment_method_id` остаются валидными при переезде. Пользователи перепривязывать карту не должны. В день cutover LMS cron отключается (`@Scheduled` комментируется в `PaymentSchedulerService.java:121-130`), наш включается. Два cron'а одновременно = двойное списание.

**Обработка `payment_credentials` (класс доступа §5 П6.1):** `autopay_data` содержит `payment_method_id` + `card.last4`. Зная эти данные плюс YooKassa API-ключ, можно инициировать произвольное списание. Поэтому: writer — только `payment-scheduler` и `incremental-sync`; reader — только `payment-scheduler` (для формирования запроса); Metabase и Бот — `REVOKE`; при отмене пользователем — вызов YooKassa unlink API + обнуление колонки; рассмотреть шифрование at-rest на Ф11 WP-246.

**Миграция:** → WP-246 Стадия 3 (Ф10–Ф16, ~14h). Gap-фиксы по ходу портирования: письмо при провале списания, retry-логика, unlink API.

---

### DB #6: aist-bot

Telegram-first среда обучения и коммуникации. 35+ физ.таблиц, но на концептуальном уровне — ~10 физ.объектов: всё остальное (FSM-state, токены, кеш, логи, проекции) — технические.

**BC:** Bot Learning & Community. **Замечание:** многие объекты (публикация, платёж, концепт) здесь — проекции из других БД. В §10 физ.схема разбита на 3 подраздела (6a/6b/6c) — на концептуальной ER оставляем одну диаграмму.

```mermaid
erDiagram
    СОЗИДАТЕЛЬ ||--|| ПОЛЬЗОВАТЕЛЬ_БОТА : "проекция (1:1)"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ ОТВЕТ_НА_ЗАДАНИЕ : "даёт"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ ОЦЕНКА : "получает"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ ПОПЫТКА_ТРЕНАЖЁРА : "проходит"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ QA_ПАРА : "задаёт"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ FEEDBACK_ЗАПИСЬ : "оставляет"
    ПОЛЬЗОВАТЕЛЬ_БОТА ||--o{ НЕДЕЛЬНЫЙ_ЦИКЛ_ЛЕНТЫ : "читает"
    ПОЛЬЗОВАТЕЛЬ_БОТА }o--o{ TELEGRAM_КАНАЛ : "участник сообщества | мониторинг"
    ЭЛЕМЕНТ_МАРАФОНА ||--o{ ПОПЫТКА_ТРЕНАЖЁРА : "основа для"

    ПОЛЬЗОВАТЕЛЬ_БОТА {
        bigint telegram_id "PK"
        uuid ory_id "ссылка на Созидателя в #1"
        date trial_started_at
        int active_days_total
    }
    ОТВЕТ_НА_ЗАДАНИЕ {
        string question_id
        int score
        timestamp создано
    }
    ОЦЕНКА {
        string тип "self | external | peer"
        jsonb данные
    }
    ПОПЫТКА_ТРЕНАЖЁРА {
        string training_type "meme | practice"
        int score
        timestamp начата
    }
    ЭЛЕМЕНТ_МАРАФОНА {
        string content_type "meme | question | lesson"
        int номер_в_марафоне
    }
    QA_ПАРА {
        text вопрос
        text ответ
        bool helpful
    }
    FEEDBACK_ЗАПИСЬ {
        string категория
        string статус "new | classified | resolved"
    }
    НЕДЕЛЬНЫЙ_ЦИКЛ_ЛЕНТЫ {
        int номер_недели
        timestamp создана
    }
    TELEGRAM_КАНАЛ {
        bigint channel_id "внешний"
        string название
    }
    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
```

**Примечания.**
- **Пользователь бота** ≠ Созидатель: это проекция Созидателя в контексте Telegram-клиента (локальные атрибуты триала, счётчик активных дней). Связь 1:1 по `ory_id`.
- 🔗 «Участник сообщества» (`COMMUNITY_MEMBERS`) и «Мониторируемый канал» (`CHANNEL_MONITORS`) — связи Пользователь↔Канал с разным scope (участие vs слежение ботом). На ER — одно ребро «участник сообщества | мониторинг» с Telegram-каналом.
- 🔗 «Напоминание» (`REMINDERS`) = связь Пользователь↔Триггер с атрибутами (тип, время); на физ.схеме есть таблица, на концептуальной ER — не узел (реестр §7.0.2 относит к 🔗).
- **Сессия ленты** (`FEED_SESSIONS`) = связь Пользователь↔Недельный цикл (факт захода в конкретную неделю); не показана как узел.
- ⚠️ Технические (не показаны на ER): `USER_STATE`, `FSM_STATES` (FSM-состояние), `USER_SESSIONS`, `ORY_TOKENS`, `DT_TOKENS` (OAuth-носители), `OAUTH_PENDING_STATES`, `CONTENT_CACHE`, `TRAINING_PROGRESS` (state-файл), `ACTIVITY_LOG`, `ERROR_LOGS`, `REQUEST_TRACES` (копии → #8), `PENDING_FIXES` (перенесено → #8 WP-244), `NOTIFICATION_LOG`, `CHANNEL_MENTIONS_LOG`, `SERVICE_USAGE`.
- ⚠️ Кандидаты на миграцию (остаются физ.таблицами в #6 до миграции, концептуально принадлежат другим БД):
  - `PUBLISHED_POSTS`, `SCHEDULED_PUBLICATIONS`, `DISCOURSE_ACCOUNTS` → #9 content-pipeline
  - `SEMINAR_PAYMENTS`, `WORKSHOP_PAYMENTS` → #5 payment-registry
  - `CONVERSION_EVENTS` → #4 activity-hub
  - `TIER_EVENTS` → #4 (или #1)
  - `BOT_SUBSCRIPTIONS` → проекция/кеш из #1 `SUBSCRIPTION_GRANTS`
- ⚠️ `USER_ACCESS`, `FEEDBACK_REPORTS` — требуют аудита владельца (Ф13).

---

### DB #7: metabase

BI-инструмент Metabase. Сам прикладных данных не хранит — читает #5 и #4 через RLS.

**BC:** BI & Analytics. 4 пользовательских физ.объекта из ~171 служебной таблицы Metabase.

```mermaid
erDiagram
    КОЛЛЕКЦИЯ_ДАШБОРДОВ ||--o{ КОЛЛЕКЦИЯ_ДАШБОРДОВ : "вложенная (parent)"
    КОЛЛЕКЦИЯ_ДАШБОРДОВ ||--o{ ДАШБОРД : "содержит"
    ДАШБОРД ||--o{ КАРТА_ЗАПРОС : "состоит из"
    АНАЛИТИК }o--o{ ДАШБОРД : "просматривает | редактирует"

    КОЛЛЕКЦИЯ_ДАШБОРДОВ {
        string название "напр. Финансы, Обучение"
    }
    ДАШБОРД {
        string название "напр. Финансы 2026 Q2"
    }
    КАРТА_ЗАПРОС {
        string название
        text dataset_query "SQL / MBQL"
        string визуализация "bar | line | table"
    }
    АНАЛИТИК {
        string email "не-Ory, локальный Metabase"
        bool суперпользователь
    }
```

**Примечания.**
- «Данные» дашбордов живут в #5 (финансы) и #4 (события), Metabase их только визуализирует через SQL-запросы с RLS. На концептуальной ER #7 эти связи не показаны (они — между БД, не внутри #7).
- ⚠️ ~167 других служебных таблиц (`metabase_field`, `metabase_query_execution`, `metabase_session`, etc.) — технические, не на ER.

---

### DB #8: health

Операционное здоровье платформы. Cross-cutting — не принадлежит ни одному продуктовому сервису.

**BC:** Platform Health & Observability. Физ.объекты — инфраструктурные, не доменные.

```mermaid
erDiagram
    МИКРОСЕРВИС_ДЕПЛОЙМЕНТ ||--o{ ИНЦИДЕНТ : "страдает от"
    ИНЦИДЕНТ ||--o| ЗАДАНИЕ_AUTO_FIX : "порождает"

    МИКРОСЕРВИС_ДЕПЛОЙМЕНТ {
        string имя "aist-bot@Railway | gateway-mcp@Cloudflare | knowledge-mcp@Railway"
        string url "endpoint для пинга"
        string тип_проверки "http | tcp"
        bool активен
    }
    ИНЦИДЕНТ {
        timestamp начало
        timestamp завершение
        int длительность_мин
        string severity "warning | critical"
        text описание
    }
    ЗАДАНИЕ_AUTO_FIX {
        text diagnosis "Claude diagnosis"
        string статус "pending | approved | rejected | applied"
        timestamp создано
    }
```

**Примечания.**
- **Микросервис-деплоймент** — инфраструктурный физ.объект: конкретный запущенный экземпляр сервиса на конкретной платформе. Экземпляру можно дать имя («aist-bot production» на Railway) и показать пальцем (URL endpoint).
- ⚠️ Технические сущности (лог/снимок, не показаны): `UPTIME_CHECKS` (пинги, TTL 90d), `ANTHROPIC_STATUS_SNAPSHOTS` (снимки чужого status page), `ERROR_LOGS` (перенесено из #6 WP-45), `REQUEST_TRACES` (OTel).
- **Источник истины** для status.anthropic.com — внешний. Снимки — периодический fetch, не физ.объект домена.

---

### DB #9: content-pipeline

Конвейер публикаций: черновик → одобрение → рендер → публикация в соцсети. Единственная БД с мульти-канальными публикациями. Токены соц.сетей — класс `payment_credentials` (строгая изоляция).

**BC:** Content Pipeline. Референс: `WP-155`, `DP.SC.121` (TBD).

```mermaid
erDiagram
    СОЗИДАТЕЛЬ ||--o{ ЗАДАНИЕ_ПУБЛИКАЦИЯ : "автор"
    СОЗИДАТЕЛЬ }o--o{ КАНАЛ_СОЦСЕТИ : "OAuth-связка (токены)"
    ЗАДАНИЕ_ПУБЛИКАЦИЯ }o--o{ АССЕТ : "включает"
    ЗАДАНИЕ_ПУБЛИКАЦИЯ ||--o| РАСПИСАНИЕ_ПУБЛИКАЦИИ : "запланировано на"
    ЗАДАНИЕ_ПУБЛИКАЦИЯ ||--o{ ПУБЛИКАЦИЯ : "порождает"
    ПУБЛИКАЦИЯ ||--|| КАНАЛ_СОЦСЕТИ : "размещена в"

    СОЗИДАТЕЛЬ {
        uuid ory_id "внешний — из #1"
    }
    ЗАДАНИЕ_ПУБЛИКАЦИЯ {
        text черновик_текста
        string статус "draft | approved | rendering | published | failed"
        timestamp создано
    }
    АССЕТ {
        string url
        string тип "video | audio | image"
        jsonb metadata
    }
    РАСПИСАНИЕ_ПУБЛИКАЦИИ {
        timestamp слот
        bool подтверждено
    }
    ПУБЛИКАЦИЯ {
        string внешний_id "ID поста в TG/YouTube/X"
        string url
        timestamp опубликовано
    }
    КАНАЛ_СОЦСЕТИ {
        string тип "telegram | youtube | x | discourse"
        string название "@aist_me, IWE-канал"
        string external_id "ID канала у провайдера"
    }
```

**Примечания.**
- **Задание-публикация (Job)** — физ.объект жизненного цикла (от черновика до финала). Не «сообщение», а контракт на публикацию с возможностью предварительного одобрения.
- **Публикация (Post)** — результат успешного Job: уже опубликованный пост в конкретный канал с URL. Один Job может породить несколько Публикаций (кросс-постинг в несколько каналов).
- 🔗 «OAuth-связка Созидатель↔Канал» = связь с атрибутами (токены, scope); на физ.схеме — `CHANNEL_ACCOUNTS`. Класс `payment_credentials` (§5 П6.1).
- ⚠️ `PUBLICATION_EVENTS` (факт успеха/неудачи) = событие, дублируется в #4 activity-hub через API.
- **Мульти-тенант** (PMB-4): структура подразумевает одного владельца (tenant=Tseren) в MVP; мульти-тенант — после 24 апр.

</details>

---

<details>
<summary><b>3. Обзорная диаграмма</b></summary>

Сплошная стрелка = запись, пунктир = чтение (RO). Детали каждого потока — §7.1–7.5. Три параллельные колонки: слева — источники данных, в центре — 9 баз Neon, справа — агенты и сервисы-читатели.

```mermaid
flowchart LR
    classDef ext     fill:#f5f0e8,stroke:#b0956a,color:#333
    classDef db_gw   fill:#dbeafe,stroke:#3b82f6,color:#1e3a5f
    classDef db_prod fill:#dcfce7,stroke:#22c55e,color:#14532d
    classDef db_infra fill:#f3f4f6,stroke:#9ca3af,color:#374151
    classDef reader  fill:#fef9c3,stroke:#ca8a04,color:#713f12

    subgraph SRC ["Источники данных"]
        direction TB
        LMS(["LMS Aisystant"]):::ext
        Club(["Discourse Club"]):::ext
        Waka(["WakaTime"]):::ext
        GHApp(["GitHub App"]):::ext
        Pay(["YooKassa / Stripe / TG Stars"]):::ext
        PayRcv(["Payment Receiver\n(CF Worker, WP-246)"]):::reader
        Bot(["AIST Bot"]):::ext
        WebApp(["Web App"]):::ext
        IWE(["IWE / exocortex"]):::ext
        Pinger(["Пингер сервисов\n(каждые 5 мин)"]):::db_infra
        Kratos(["Ory Kratos\n(идентичность)"]):::ext
    end

    subgraph NEON ["Neon (9 баз)"]
        direction TB
        PC[("#1 platform-core\nidentity · подписки")]:::db_gw
        DT[("#2 digital-twin\nпрофиль пользователя")]:::db_prod
        KN[("#3 knowledge\nдокументы · концепты")]:::db_prod
        AH[("#4 activity-hub\nсобытия Bronze→Gold")]:::db_prod
        PR[("#5 payment-registry\nфинансы")]:::db_prod
        AB[("#6 aist-bot\nFSM · OAuth-сессии · QA")]:::db_prod
        MB[("#7 metabase\nслужебные таблицы")]:::db_infra
        HL[("#8 health\nдоступность · ошибки")]:::db_infra
        CP[("#9 content-pipeline\nпубликации · OAuth соцсетей")]:::db_prod
    end

    subgraph READ ["Агенты и сервисы"]
        direction TB
        Keto(["Ory Keto\n(права доступа)"]):::ext
        GW(["gateway-mcp"]):::reader
        KnMCP(["knowledge-mcp"]):::reader
        PkMCP(["personal-knowledge-mcp"]):::reader
        DtMCP(["digital-twin-mcp"]):::reader
        Profiler(["Профайлер"]):::reader
        Tailor(["Портной"]):::reader
        Navi(["Навигатор"]):::reader
        Composer(["Composer MCP\n(в разработке)"]):::reader
        Metabase(["Metabase\n(дашборды)"]):::reader
        Grafana(["Grafana\n(доступность)"]):::reader
        Langfuse(["Langfuse\n(журнал AI)"]):::reader
    end

    %% ── Источники → Neon ────────────────────────────────────────
    LMS -->|"события"| AH
    Club -->|"события"| AH
    Waka -->|"события"| AH
    GHApp -->|"установки"| KN
    Pay -->|"webhook"| PayRcv
    PayRcv -->|"платежи + дедупликация"| PR
    PayRcv -.->|"forward (стадия 1)"| LMS
    Bot -->|"состояние · токены"| AB
    Bot -->|"события"| AH
    Bot -->|"ошибки"| HL
    WebApp -->|"события"| AH
    IWE -->|"события"| AH
    Pinger -->|"доступность · инциденты"| HL
    Kratos -->|"webhook: регистрация"| PC

    %% ── Агенты ↔ Neon ───────────────────────────────────────────
    GW -.->|"проверка JWT"| Kratos
    GW -.->|"проверка прав"| Keto
    GW -.->|"проверка подписки"| PC
    GW -->|"проброс к сервисам"| KnMCP
    GW -->|"проброс к сервисам"| DtMCP
    KnMCP -.->|"поиск"| KN
    KnMCP -->|"индексация"| KN
    PkMCP -->|"личные документы"| KN
    PkMCP -->|"событие записи"| AH
    DtMCP -.->|"профиль"| DT
    Profiler -.->|"история обучения"| AH
    Profiler -->|"показатели развития"| DT
    Tailor -.->|"профиль"| DT
    Navi -.->|"профиль"| DT
    Composer -.->|"состояние FSM"| AB
    Metabase -.->|"финансы (RLS)"| PR
    Metabase -.->|"события (без PII)"| AH
    Grafana -.->|"только чтение"| HL
    Bot -->|"журнал запросов"| Langfuse
    GW -->|"журнал запросов"| Langfuse

    %% ── Межбазовые потоки ───────────────────────────────────────
    PR -->|"синхронизация подписок"| PC
    AH -->|"связка chat_id → ory_id"| PC
    KN -->|"освоение концептов"| DT
    PR -.->|"финансы (RLS)"| MB
    AH -.->|"события (без PII)"| MB
    HL -.->|"доступность"| MB

    %% ── #9 content-pipeline ─────────────────────────────────────
    Bot -->|"UI конвейера"| CP
    CP -.->|"OAuth токены (изолированно)"| CP
    CP -->|"публикация в соцсети"| ExtSocial(["Telegram/VK/YouTube\n(внешние каналы)"]):::ext
    CP -->|"событие публикации"| AH
```

</details>

---

<details open>
<summary><b>4. Контекст и статус</b></summary>

**Зачем этот документ:** мы проектируем новую архитектуру всех систем платформы Aisystant. Структура баз данных — фундамент: от неё зависит безопасность, независимость команд, масштабируемость и возможность замены отдельных сервисов. Этот документ — source-of-truth по тому, какие данные где живут, как сервисы связаны и какие проблемы ещё нужно решить.

**Текущее состояние (апр 2026):** все таблицы живут в одной базе `platform` в Neon. Разделение на 9 баз — следующий РП (~20-40h, не начат).

**Что зафиксировано:**
- Принципы разделения данных (7 инвариантных правил)
- Карта 9 баз + внешние системы и их связи
- ERD всех таблиц включая новые (audit log, trace_id, тиры, шифрование токенов)
- Принятые архитектурные решения по безопасности, наблюдаемости, масштабированию

---

**Как читать (для команды):**

Ссылка на документ: **https://github.com/TserenTserenov/PACK-digital-platform/blob/main/pack/digital-platform/02-domain-entities/DP.ARCH.004-neon-data-architecture.md**

**В браузере (GitHub):** разделы открываются кликом на заголовок ▶ / ▼.

**В VS Code:** открыть файл → нажать `Cmd+Shift+V` (Mac) или `Ctrl+Shift+V` (Windows/Linux) → откроется Markdown Preview с интерактивными спойлерами.

**Навигация:** §6 Карта баз — быстрый ориентир. Нужна конкретная таблица → §10 Справочник (Ctrl/Cmd+F по имени таблицы). Нужно понять связи — §7 Диаграмма или §8 кто читает/пишет.

</details>

---

<details open>
<summary><b>5. Принципы</b></summary>

**П1. 1 сервис = 1 база** (не схема)
Каждый сервис имеет собственную БД с собственными credentials. Это гарантирует, что компрометация одного сервиса не открывает данные других.

**П2. FK только внутри одной базы**
Ссылки между сервисами — только `ory_id`/`telegram_id` без FK constraint. Это позволяет деплоить и мигрировать каждую базу независимо.

**П3. Схема = namespace для роли-администратора**
Внутри базы схемы разграничивают доступ ролей (финансист видит `finance.*`), а не изолируют сервисы — чтобы не создавать ложное ощущение безопасности.

**П4. `ory_id` — глобальный ключ для платформ-сервисов**
Платформ-базы хранят `ory_id` без FK — Ory Kratos остаётся единственным source of truth идентичности. Бот до OAuth работает по `telegram chat_id`; разрешение `chat_id → ory_id` — через `activity-hub.IDENTITY_MAP` (WP-187).

**П5. Activity Hub — проектировать под замену**
OAuth-конфигурация (`USER_INTEGRATIONS`) хранится в `platform-core` — чтобы при смене движка событий (ClickHouse/TimescaleDB) не потерять настройки интеграций.

**П6. Платежи — изолированный реестр**
`payment-registry` — единственная база с финансовыми транзакциями. Metabase читает только через `metabase_reader` с RLS (агрегаты без PII), прямого доступа у других сервисов нет.

**П6.1. Классы чувствительности данных (→ WP-212 Ф9, B7.3)**

Для принятия решений о RLS, ролях и шифровании различать четыре класса:

| Класс | Примеры | Писатель | Читатель | Требования |
|-------|---------|----------|----------|-------------|
| `public` | агрегаты, справочники (каналы, валюты, статусы) | сервисы | все роли, Metabase | — |
| `PII` | email, telegram_id, ory_id, имя, чат | владеющий сервис | владелец + агрегаторы с обезличиванием | RLS по user_id; Metabase — только агрегаты |
| `payment_credentials` | `autopay_data` (payment_method_id YooKassa), токены рекуррентных списаний | `payment-scheduler` + `incremental-sync` (до cutover) | `payment-scheduler` (формирование запроса в YooKassa) | Строже PII: REVOKE для Metabase и Бота; аудит SELECT; рассмотреть шифрование at-rest; unlink + обнуление при отмене пользователем |
| `secrets` | API-ключи, webhook secrets, Ory client secrets | деплой | процесс, владеющий секретом | Не в Neon — в Railway/CF secrets |

**Правило класса `payment_credentials`:** зная значение + API-ключ провайдера (YooKassa), можно инициировать произвольное списание с карты. Поэтому: (1) writer и reader на уровне ролей явно ограничены; (2) никаких агрегаций/JOIN с участием этой колонки в Metabase; (3) при отмене автопродления пользователем — unlink токена в YooKassa + обнуление колонки в Neon. До cutover WP-246 Ф15 source-of-truth для `autopay_data` — LMS; после — Neon.

**П7. `SUBSCRIPTION_GRANTS` в `platform-core` — gateway-паттерн**
`payment-registry` синхронизирует гранты в `platform-core` через cron (push-based sync). Все сервисы проверяют права только здесь — единая точка авторизации, не дублирующая логику.

**Инвариант двойной проверки:** gateway-mcp выполняет `verifyJWT` + `checkSubscription` **до** фанаута в downstream MCP (knowledge-mcp, digital-twin-mcp и др.). Если хотя бы одна проверка не прошла — gateway отвечает 401/403 немедленно, не обращаясь к downstream сервисам. Downstream сервисы не реализуют собственную проверку подписки — это исключительная ответственность gateway.

**П8. Health-данные отделены от системы**

Данные о здоровье системы (логи ошибок, pending-фиксы, метрики аптайма, инциденты, трассы) хранятся в **БД #8 health**, а не в самой системе, которая их генерирует.

**Причины:**
- **Диагностика при отказе.** Если система упала, её БД тоже недоступна → диагностика из неё невозможна. #8 — независимый контур.
- **Разный lifecycle.** Health-данные append-only, высокий объём, редкое чтение из production-пути. Смешивать с domain-данными = засорять рабочую БД.
- **OwnerIntegrity.** Владелец health-домена — БД #8, не система-источник. Смешение = нарушение bounded context.

**Применимо ко всем системам:** бот (#6), gateway-mcp, knowledge-mcp (#3), content-pipeline (#9), digital-twin (#2), activity-hub (#4), platform-core (#1), payment-registry (#5), metabase (#7).

**Контрпример (нарушение):** PENDING_FIXES жил в aist-bot (#6) как очередь авто-фиксов — сломанный бот не мог обрабатывать свою же очередь. Перенесено в #8 (WP-244).

**Операционное следствие:** При проектировании нового сервиса — НЕ добавлять `*_errors` / `*_health` / `*_incidents` / `*_fixes` таблицы в его БД. Отправлять в #8 через writer-паттерн (или прямой SQL в #8 из error_classifier).

</details>

---

<details open>
<summary><b>6. Карта баз данных</b></summary>

```
Neon Project: aisystant
│
├── DB #1: platform-core   ← USER_IDENTITIES + SUBSCRIPTION_GRANTS
│                             + GITHUB_CONNECTIONS + GOOGLE_CALENDAR_CONNECTIONS
│                             + USER_INTEGRATIONS + BACKEND_REGISTRY
│                             + PRODUCTS + MENTOR_ASSIGNMENTS
│                             + points.point_transactions + points.points_rules
│                             + points.points_multipliers (Points Engine schema)
│                             + directus.* (~15 таблиц)
│
├── DB #2: digital-twin    ← DIGITAL_TWINS + LEARNER_CONCEPT_MASTERY
│
├── DB #3: knowledge       ← DOCUMENTS + CONCEPTS + CONCEPT_EDGES
│                             + CONCEPT_MISCONCEPTIONS
│                             + GITHUB_INSTALLATIONS + USER_SOURCES
│
├── DB #4: activity-hub    ← RAW_EVENTS (partitioned) + USER_EVENTS
│                             + LEARNING_HISTORY (Gold)
│                             + IDENTITY_MAP + SYNC_LOG + QUARANTINED_EVENTS
│                             ⚠ кандидат на замену ClickHouse/TimescaleDB
│
├── DB #5: payment-registry ← FINANCE_PAYMENTS + FINANCE_PAYMENTS_AUDIT_LOG
│                             + PROCESSED_WEBHOOKS (WP-246)
│                             + PAYMENTS_SYNC_STATE + SUBSCRIPTION_GRANTS_SYNC_STATE
│                             + IMPORT_STAGING_PAYMENT + IMPORT_STAGING_CHARGEOFF
│
├── DB #6: aist-bot        ← 35+ таблиц (FSM, тренажёры, фид, марафоны,
│                             токены, публикации, оплаты, наблюдаемость)
│                             ⚠ 10 таблиц — кандидаты на миграцию в #1/#4/#5/#8
│                             Полный список → §10 #6
│
├── DB #7: metabase        ← ~171 служебная таблица Metabase
│                             читает payment-registry (metabase_reader + RLS)
│                             читает activity-hub (read-only)
│
└── DB #8: health          ← SERVICE_REGISTRY + UPTIME_CHECKS + UPTIME_INCIDENTS
                              + ANTHROPIC_STATUS_SNAPSHOTS
                              + ERROR_LOGS + REQUEST_TRACES + PENDING_FIXES
                              читает Grafana (read-only)

Вне Neon:
  Ory Kratos   ← идентичность, source of truth по ory_id
  Ory Keto     ← access control (роли/разрешения); данные в Neon не хранит
  Langfuse     ← AI observability (traces, evals); внешний сервис
  Grafana      ← дашборды мониторинга; читает #8 health (read-only)
  LMS          ← учебная платформа Aisystant; read-only, синхронизация через staging
  Club         ← сообщество; события → #4 activity-hub (source='club')
  Web App      ← Vue.js клиент; серверного state в Neon нет
  CF Workers   ← gateway-mcp, knowledge-mcp; stateless, пишут/читают в Neon по API
  Railway      ← хостинг сервисов; инфра-уровень, данных в Neon не хранит
  WakaTime     ← коллектор активности; события → #4 activity-hub (source='iwe')
  GitHub App   ← коллектор репо; установки → #3 GITHUB_INSTALLATIONS

⚠ Текущее состояние (апр 2026): все таблицы живут в одной базе `platform` в Neon.
  Разделение на 9 баз — следующий РП (~20-40h, не начат).
```

</details>

---

<details open>
<summary><b>7. Архитектура Neon и связи с системами</b></summary>

**Структура раздела:**
- **7.0.2** — реестр физ.объектов по БД (основа для ERD вверху документа)
- **7.1** — 9 баз и внешние системы (что куда пишет/читает)
- **7.2** — реестр всех систем (таблица)
- **7.3** — поток идентичности (Ory → Gateway → platform-core)
- **7.4** — поток событий → ЦД → персональное руководство
- **7.5** — связи между базами данных (только межбазовый граф)

Все стрелки — API / события / cron, не FK. Обзорная диаграмма, карта 9 БД и ERD по базам — в начале документа.

---

### 7.0.2 Физ.объекты по БД (реестр)

> **Цель:** исчерпывающий список объектов физ.мира для каждой из 9 БД. Основа для ERD (раздел вверху документа, WP-228 Ф9).
>
> **Методология:** чек-лист `DP.METHOD.040 §4`. Критерии физ.объекта:
> 1. Экземпляру можно дать имя собственное («Созидатель Иванов», «Курс СМ-2026-S2», «Платёж P-12345»).
> 2. Можно «показать пальцем» на конкретный экземпляр.
> 3. Не техническая таблица (`*_log`, `*_session`, `*_cache`, `*_snapshot`).
> 4. Не M:N-связка без собственных атрибутов.
>
> **Источники:** Pack (01A-bounded-context, DP.D.050, DP.ROLE.001, DP.ECON.001, DP.ARCH.003, DP.D.034), DP.ARCH.004 §10 (таблицы), WP-155 (CP), WP-227 (ЦД), WP-151 (квалификация).
>
> **Колонки:** Физ.объект / Описание / Реализация (таблица или план) / Класс (✅ физ.объект, 🔗 связь с атрибутами, ⚠️ технический, ❓ спорное — нужен ревью) / Источник в Pack.

#### БД #1 platform-core

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Созидатель** | Физ.лицо, участник экосистемы развития | USER_IDENTITIES | ✅ | 01A, DP.D.050, DP.ROLE.001 |
| **Продукт-экземпляр** | Конкретный курс/семинар/подписка-план с кодом (напр. «СМ-2026-S2», «Подписка-годовая-2026») | PRODUCTS | ✅ | DP.ECON.001 |
| **Подписка-контракт** | Конкретный грант доступа созидателю к продукту с датами действия | SUBSCRIPTION_GRANTS | ✅ | DP.ECON.001, WP-231 |
| **Персональный MCP-бэкенд** | BYOB-сервер, зарегистрированный созидателем (backend_url, tool_prefix) | BACKEND_REGISTRY | ✅ | WP-189 |
| **Присвоение квалификации** | Событие: созидателю присвоен уровень шкалы (Интересант→…→Общественный деятель) с датой, обоснованием, экзаменатором | `platform.qualification_events` (целевое, источник LMS `qualification_level_event`, WP-151 Ф7a смаплено 67/67) | ✅ событие-факт | WP-151, LMS |
| **Уровень квалификации-справочник** | 11 ступеней шкалы с кодом, порядком, метаданными | `platform.qualification_levels` (целевое) | ✅ справочник | WP-151, system-school.ru/qualification |
| **Правило начисления баллов** | Справочник: какое событие → сколько базовых баллов (2-69) × категория (×1/×2/×3/×5) | `platform.points.point_rules` | ✅ справочник | DP.ECON.001, WP-121 Ф1 |
| **Транзакция баллов** | Append-only факт начисления/списания баллов созидателю за конкретное событие | `platform.points.point_transactions` (UNIQUE event_id, source-of-truth) | ✅ событие-факт | DP.ECON.001, WP-121 Ф1 |
| **Множитель квалификации** | Справочник множителей балла по уровню (×1.0-×5.0) + daily_cap (100-1000) | `platform.points.qualification_multipliers` | ✅ справочник | DP.ECON.001, WP-121 Ф1 |
| Наставник-назначение | Привязка созидателя-наставника к продукту с ролью (TM1/TM2/TM3) | MENTOR_ASSIGNMENTS | 🔗 связь «Созидатель(наставник) ↔ Продукт» с атрибутом role | DP.ROLE.001 |
| GitHub-подключение (публикация) | OAuth-связка созидателя с GitHub для публикации (бот пишет в репо) | GITHUB_CONNECTIONS | 🔗 связь «Созидатель ↔ GitHub-аккаунт» scope=repo(write), токены | WP-187 |
| Google Calendar-подключение | OAuth-связка созидателя с Google Calendar | GOOGLE_CALENDAR_CONNECTIONS | 🔗 связь | WP-232 |
| WakaTime-интеграция | OAuth-сессия для сбора метрик кода | USER_INTEGRATIONS | 🔗 связь | — |
| Баланс баллов (кеш) | Агрегат-кэш баланса созидателя (пересчитывается из transactions) | `platform.points.point_balances` | ⚠️ технический кеш (source-of-truth = transactions) | DP.ECON.001 |
| Событие смены тира | Факт перехода T0→T1 и т.д. | TIER_EVENTS | ⚠️ это **событие**, его место в #4 activity-hub (запланировано) | — |

#### БД #2 digital-twin

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Цифровой двойник** | Модель-снимок созидателя, 3 слоя (базовые / вовлечённость / производные) | DIGITAL_TWINS | ✅ (один экземпляр на созидателя) | DP.ARCH.003, WP-227 |
| **Показатель развития** | Конкретный индикатор (IND.3.*) с значением в момент времени для конкретного созидателя | (колонки JSONB в DIGITAL_TWINS) | ✅ слабая сущность (существует в контексте двойника) | DP.ARCH.003 §5 |
| **Стадия развития** | Вычисленное состояние созидателя (Random/Practicing/Systematic/Disciplined/Proactive) на дату | (колонка stage в DIGITAL_TWINS) | ✅ | DP.D.050 |
| Освоение концепта | Факт: созидатель освоил концепт с вероятностью p(known) | LEARNER_CONCEPT_MASTERY | 🔗 связь «Созидатель ↔ Концепт» с атрибутами (mastery 0.0-1.0, confidence) | WP-151, WP-208 |
| Запуск профайлера | Факт прогона обновления ЦД: дата, вход, дельта показателей, результат | `digital-twin.profiling_runs` (план) | ⚠️ технический лог в MVP; вынесение в домен-событие — **PMB-7** | WP-218 |

#### БД #3 knowledge

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Документ** | Конкретный текст в векторной БД (с путём, эмбеддингом, метаданными) | DOCUMENTS | ✅ | knowledge-mcp |
| **Концепт** | Конкретное понятие (напр. «Экзокортекс», «BKT», «Systems Thinking») | CONCEPTS | ✅ | WP-170, 01A |
| **Концептное заблуждение** | Типовая ошибка понимания концепта | CONCEPT_MISCONCEPTIONS | ✅ | CAT.001 |
| **Источник индексации** | Конкретный репо/URL, подключённый созидателем для ingest | USER_SOURCES | ✅ | knowledge-mcp |
| GitHub-установка (индексация) | Конкретная GitHub App installation для индексации репо | GITHUB_INSTALLATIONS | 🔗 связь «Созидатель ↔ GitHub-аккаунт» scope=repo(read), для ingest | WP-187 |
| Ребро графа концептов | prerequisite / related / part_of / contradicts | CONCEPT_EDGES | 🔗 связь «Концепт ↔ Концепт» с типом | — |
| Обратная связь релевантности | Оценка созидателем релевантности документа в поиске | RETRIEVAL_FEEDBACK | ⚠️ это **событие**, не объект | knowledge-mcp |

#### БД #4 activity-hub

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Сырое событие (Bronze)** | Неконсолидированный факт от внешней системы (LMS/Club/WakaTime) с атрибутами источника | RAW_EVENTS | ✅ | WP-109 |
| **Событие созидателя (Silver)** | Нормализованное событие с атрибуцией конкретного созидателя | USER_EVENTS | ✅ | WP-109 |
| **Факт обучения (Gold)** | Агрегированный факт для профайлера (концепт, корректность, длительность) | LEARNING_HISTORY | ✅ | миграция 007 |
| **Событие конверсии** | Факт шага воронки созидателя (chat_id, trigger_type C1-C7, milestone, action shown/clicked/dismissed). Чистое событие без payment_id/amount. | CONVERSION_EVENTS | ✅ событие-факт (принято 20 апр: → #4 activity-hub) | Решение 20 апр 2026 (Р3) |
| Идентичность-маппинг | chat_id ↔ ory_id (связка до OAuth) | IDENTITY_MAP | 🔗 связь «Telegram-id ↔ Созидатель», слабая идентификация | WP-109 |
| Запись карантина | Событие, не прошедшее валидацию | QUARANTINED_EVENTS | ⚠️ **технический** sink | — |
| Журнал запусков коллекторов | Старт/финиш/статус синхронизации | SYNC_LOG | ⚠️ **технический** лог | — |

#### БД #5 payment-registry

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Платёж** | Конкретная транзакция с суммой, валютой, источником (YooKassa/Stripe/TG Stars/Paybox), типом (seminar/workshop/subscription) | FINANCE_PAYMENTS, SEMINAR_PAYMENTS, WORKSHOP_PAYMENTS | ✅ | WP-183 |
| **Автосписание-договор** | Сохранённый `payment_method_id` + график автосписаний для созидателя | AUTOPAY, AUTOPAY_DATA | ✅ контракт-объект (класс payment_credentials) | WP-246, §5 П6.1 |
| Ватермарк импорта | Позиция инкрементальной синхронизации | PAYMENTS_SYNC_STATE, SUBSCRIPTION_GRANTS_SYNC_STATE | ⚠️ **технический** state | WP-183, WP-231 |
| Webhook-дедупликация | event_id обработанных webhook'ов | PROCESSED_WEBHOOKS | ⚠️ **технический** idempotency | WP-246 |
| Landing zone | Временное хранение для импорта | IMPORT_STAGING_PAYMENT, IMPORT_STAGING_CHARGEOFF | ⚠️ **технический** | WP-183 |
| Аудит-запись изменений статуса | Append-only лог | FINANCE_PAYMENTS_AUDIT_LOG | ⚠️ **технический** лог (показать на ER нельзя, только на физ.схеме) | WP-237 |
| Событие конверсии | Факт шага воронки созидателя | CONVERSION_EVENTS | ✅ Принято 20 апр: CONVERSION_EVENTS → #4 activity-hub (не платёж: chat_id, trigger_type C1-C7, milestone, action shown/clicked/dismissed) | Решение 20 апр 2026 (Р3) |

#### БД #6 aist-bot

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Пользователь бота** | Созидатель как клиент бота (telegram_id, ory_id) | USERS | ✅ проекция «Созидатель в контексте бота» | — |
| **Ответ на задание** | Конкретный ответ созидателя на задачу | ANSWERS | ✅ | — |
| **Оценка** | Конкретная самооценка или внешняя оценка | ASSESSMENTS | ✅ | — |
| **Попытка тренажёра** | Один проход тренажёра (мемы, задания) с score | TRAINING_ATTEMPTS | ✅ | — |
| **Элемент марафона** | Конкретный урок/задание марафона | MARATHON_CONTENT | ✅ | — |
| **QA-пара** | Вопрос созидателя и ответ консультанта/бота | QA_HISTORY | ✅ | WP-132 |
| **Участник сообщества** | Созидатель в Telegram-сообществе (chat_id, permissions) | COMMUNITY_MEMBERS | ✅ | — |
| **Мониторируемый Telegram-канал** | Конкретный TG-канал, за которым следит бот | CHANNEL_MONITORS | ✅ | — |
| **Детский-трек** | Конкретный учебный детский трек | TRAINING_CHILDREN | ✅ (добавлен 20 апр, Р9) | — |
| Напоминание | Конкретная запись расписания | REMINDERS | 🔗 связь «Созидатель ↔ Триггер» с атрибутами | — |
| Права доступа | Временные права, выданные ботом (scope, expiry) | USER_ACCESS | 🔗 связь «Созидатель ↔ Ресурс» с атрибутами (добавлено 20 апр, Р9) | — |
| Настройка трека | Пользовательские настройки тренажёров | TRAINING_SETTINGS | 🔗 связь «Созидатель ↔ Настройка-трека» (добавлено 20 апр, Р9) | — |
| Feedback-запись | Отзыв созидателя из бота | FEEDBACK_TRIAGE | ✅ | — |
| Недельный цикл ленты | Feed-неделя для созидателя | FEED_WEEKS | ✅ | — |
| Сессия ленты | Отдельный просмотр feed | FEED_SESSIONS | 🔗 связь «Созидатель ↔ Feed-неделя» | — |
| Прогресс тренажёра | Состояние пользователя по тренажёру | TRAINING_PROGRESS | ⚠️ **state-файл**, не физ.объект (WP-217 distinction) | — |
| FSM-состояние | Текущая позиция в автомате aiogram | FSM_STATES, USER_STATE | ⚠️ **технический** state | — |
| Кеш контента LMS | Буферизованные уроки | CONTENT_CACHE | ⚠️ **технический** кеш | — |
| OAuth-state ожидания | Временный код во время OAuth flow | OAUTH_PENDING_STATES | ⚠️ **технический** | — |
| Сессия пользователя | Chat-сессия | USER_SESSIONS | ⚠️ **технический** (связь) | — |
| Ory OAuth-токены | Encrypted access/refresh | ORY_TOKENS | ⚠️ **технический** (носитель авторизации) | WP-209, WP-234 |
| DT OAuth-токены | Encrypted DT-токены | DT_TOKENS | ⚠️ **технический** | WP-82, WP-234 |
| Уведомление | Отправленное созидателю сообщение | NOTIFICATION_LOG | ⚠️ **лог** | WP-232 |
| Лог активности | Действие в боте | ACTIVITY_LOG | ⚠️ **лог** | — |
| Лог ошибок | Ошибки бота | ERROR_LOGS | ⚠️ **лог** (копия → #8) | — |
| Трейс запроса | OTel trace | REQUEST_TRACES | ⚠️ **лог** (копия → #8) | — |
| Лог упоминаний | Упоминание в TG-канале | CHANNEL_MENTIONS_LOG | ⚠️ **лог** | — |
| Telegram Stars подписка | Бот-уровень Stars-подписка (проекция из #1 с `source=telegram_stars`) | BOT_SUBSCRIPTIONS | 🔵 проекция/кеш — source-of-truth = SUBSCRIPTION_GRANTS в #1 | — |

#### БД #7 metabase

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Дашборд** | Конкретный дашборд (напр. «Финансы 2026 Q2») | METABASE_DASHBOARDS | ✅ | WP-183 |
| **Карта-запрос** | Конкретный SQL-запрос на дашборде | METABASE_CARDS | ✅ | WP-183 |
| **Коллекция дашбордов** | Папка (напр. «Финансы», «Обучение») | METABASE_COLLECTIONS | ✅ | WP-183 |
| **Аналитик** | Пользователь Metabase (не-Ory, с email + паролем) | METABASE_USERS | ✅ | — |

*(~167 других служебных таблиц Metabase — все технические, на ER не показывать.)*

#### БД #8 health

| Физ.объект | Описание | Реализация (таблица) | Класс | Источник |
|------------|----------|----------------------|-------|----------|
| **Микросервис-деплоймент** | Конкретный запущенный экземпляр сервиса («aist-bot @ Railway prod», «gateway-mcp @ Cloudflare», «knowledge-mcp @ Railway»). Инфраструктурный физ.объект (не-доменный) | SERVICE_REGISTRY | ✅ (инфраструктурный) | WP-244 |
| **Инцидент** | Конкретный сбой с началом/концом/severity | UPTIME_INCIDENTS | ✅ | WP-244 |
| **Задание auto-fix** | Ожидающее исправление (approve/reject) | PENDING_FIXES | ✅ | WP-45 |
| Пинг (uptime check) | Один замер доступности | UPTIME_CHECKS | ⚠️ **лог** (TTL 90d) | WP-244 |
| Снимок статуса Anthropic | Слепок status.anthropic.com | ANTHROPIC_STATUS_SNAPSHOTS | ⚠️ **снимок** (не экземпляр объекта) | WP-244 |
| Ошибка сервиса | Структурированная ошибка | ERROR_LOGS | ⚠️ **лог** | WP-45 |
| Трейс запроса | OTel trace | REQUEST_TRACES | ⚠️ **лог** | WP-45 |

#### БД #9 content-pipeline

| Физ.объект | Описание | Реализация (таблица, план WP-155) | Класс | Источник |
|------------|----------|------------------------------------|-------|----------|
| **Задание-публикация (Job)** | Единица конвейера: черновик → одобрение → рендер → публикация (draft/approved/rendering/published) | PUBLICATION_JOBS | ✅ | WP-155 Ф1 |
| **Публикация (Post)** | Опубликованный пост в конкретный канал (URL, дата, контент) | PUBLISHED_POSTS | ✅ | WP-155 |
| **Канал соцсети** | Конкретный канал/аккаунт в платформе (TG-канал «@aist_me», YouTube-канал «IWE») | CHANNELS | ✅ | WP-155 |
| **Ассет** | Файл (видео/аудио/обложка) с URL и метаданными | ASSETS | ✅ | WP-155 Ф0.5 |
| **Расписание публикации** | Запланированный слот публикации | SCHEDULES, PUBLICATION_JOBS, SCHEDULED_PUBLICATIONS (legacy из бота, перенос в #9) | ✅ | WP-155 |
| Аккаунт созидателя в канале | OAuth-связка созидателя с каналом (токены в шифре) | CHANNEL_ACCOUNTS, DISCOURSE_ACCOUNTS (legacy из бота, перенос в #9) | 🔗 связь «Созидатель ↔ Канал» с токенами (payment_credentials класс) | WP-155, §5 П6.1 |
| Событие публикации | Факт успеха/неудачи публикации | PUBLICATION_EVENTS | ⚠️ **событие**, дублируется в #4 activity-hub | WP-155 |

---

**Итого физ.объектов для ER-диаграмм:**

| БД | ✅ физ.объекты | 🔗 связи | ⚠️ технические |
|----|--------------|---------|---------------|
| #1 platform-core | 9 | 4 | 2 |
| #2 digital-twin | 3 | 1 | 1 |
| #3 knowledge | 4 | 2 | 1 |
| #4 activity-hub | 4 | 1 | 2 |
| #5 payment-registry | 2 | 0 | 4 |
| #6 aist-bot | 12 | 4 | 13 |
| #7 metabase | 4 | 0 | (~167 служебных) |
| #8 health | 3 | 0 | 4 |
| #9 content-pipeline | 5 | 1 | 1 |
| **Всего** | **46** | **13** | **28+** |

**Принятые решения (19 апр после ревью Tseren):**
- POINT_TRANSACTIONS, POINT_RULES, POINT_BALANCES, QUALIFICATION_MULTIPLIERS → **#1** (схема `points.*`, не #2). Ошибка ранее.
- BOT_SUBSCRIPTIONS — проекция/кеш из #1, не отдельный физ.объект.
- GITHUB_CONNECTIONS / GITHUB_INSTALLATIONS — обе 🔗 связи «Созидатель ↔ GitHub-аккаунт» с разным scope (публикация vs индексация).
- «Роль-справочник» убран из БД #1 — живёт в Pack (PK=code), не таблица.
- «Наставник-назначение» → 🔗 (связь Созидатель-наставник ↔ Продукт с атрибутом role).
- Интересант/Определяющийся/Первокурсник/Ученик/Работник и т.д. = единая шкала квалификаций (11 ступеней), не «Лид/Покупатель». Физ.объект: **Присвоение квалификации** в #1.
- «Сервис» → **Микросервис-деплоймент** (инфраструктурный, не доменный).

**Pack ↔ БД расхождения (Ф15 — остаются):**
- `TIER_EVENTS` записан в #1, по смыслу (событие) — в #4.
- `CONVERSION_EVENTS` — ✅ Принято 20 апр: → #4 activity-hub (было записано в #5; схема подтверждает: чистое событие воронки без payment_id/amount).
- `LEARNING_HISTORY`, `USER_EVENTS` дублируются в #6 и #4 (перенос запланирован → PMB-1).

**Пробелы в бэклог (после MVP) — см. `DS-my-strategy/inbox/WP-250-plan-2026.md` раздел «Post-MVP Backlog»:**
PMB-1 миграция учебных сущностей из #6 · PMB-2 Когорта · PMB-3 Профиль персонализации · PMB-4 Мульти-тенант #9 · PMB-5 Cedar-политики · PMB-6 Выявленный мем · PMB-7 Запуск профайлера · PMB-8 Событие аудита доступа.

---

### 7.1 Девять баз и внешние системы

Какие внешние системы пишут в каждую базу и читают из неё.

```
Внешние системы              База Neon             Читают из базы
─────────────────────────    ──────────────────    ─────────────────────

Ory Kratos (webhook) ───────→ #1 platform-core ←── gateway-mcp (подписка)
Ory Keto (RBAC) ────────────→    USER_IDENTITIES    AIST Bot (права)
subscription-sync (из #5) ──→    SUBSCRIPTION_GRANTS
OAuth callbacks ────────────→    GITHUB_CONNECTIONS

digital-twin-mcp ───────────→ #2 digital-twin  ←── AIST Bot /twin
Профайлер R28 (из #4) ──────→    DIGITAL_TWINS      Навигатор
LMS (степень DEG, ручная) ──→    LEARNER_CONCEPT_    Портной
                                 MASTERY

knowledge-mcp ingest ───────→ #3 knowledge     ←── knowledge-mcp search
GitHub App webhook ─────────→    DOCUMENTS          IWE / Claude Code
                                 CONCEPTS

LMS + Club + WakaTime ──────→ #4 activity-hub  ←── Профайлер R28
AIST Bot + IWE/exocortex ───→    RAW_EVENTS         Metabase (без PII)
transform-worker ───────────→    USER_EVENTS
                                 LEARNING_HISTORY

Payment Receiver (CF Worker)→ #5 payment-reg.  ←── Metabase (RLS)
  (webhook от YooKassa/     →    FINANCE_PAYMENTS   subscription-sync cron
   Stripe/TG Stars/Paybox)  →    PROCESSED_WEBHOOKS
incremental-sync (Aisystant)→    (стадия 2: только сверка)
Directus (ручные правки) ───→

AIST Bot (только бот) ──────→ #6 aist-bot      ←── AIST Bot
                                 USER_STATE          Composer MCP
                                 ORY_TOKENS

Metabase internal ──────────→ #7 metabase      ←── Metabase UI
                                 ~171 служебных     (дашборды читают #5, #4)

uptime-collector (GHA) ─────→ #8 health        ←── Grafana (read-only)
AIST Bot (error_handler) ───→    SERVICE_REGISTRY   алерты → TG bot
Anthropic Status API ───────→    UPTIME_CHECKS
                                 ERROR_LOGS
                                 REQUEST_TRACES

AIST Bot (UI конвейера) ────→ #9 content-pipe. ←── Bot (пуск заданий)
ContentPipeline Worker ─────→    PUBLICATION_JOBS   Metabase (RO аналитика)
OAuth callbacks соцсетей ───→    CHANNEL_ACCOUNTS
                                 PUBLISHED_POSTS
                                 ASSETS
                                 SCHEDULES
```

---

### 7.2 Реестр всех систем

> **Легенда:** ✅ наша инфраструктура · ✅ внешний — сторонний сервис (данные в Neon не хранит) · 🟡 в разработке · 🔲 запланировано

| Система | Статус | Читает из Neon | Пишет в Neon |
|---------|--------|---------------|-------------|
| Web App | ✅ | DT, KN (через GW) | events → AH |
| AIST Bot | ✅ | PC, DT, AB, PR | AB, AH, PR, DT, HL |
| IWE / Claude Code | ✅ | KN, DT (через GW) | KN |
| gateway-mcp | ✅ | PC (subscription), KN (github_installations, user_sources) | KN (GitHub App OAuth → github_installations, user_sources) |
| knowledge-mcp | ✅ | KN | KN |
| digital-twin-mcp | ✅ | DT | DT |
| personal-knowledge-mcp | ✅ | KN | KN |
| Профайлер R28 | ✅ | AH | DT (IND.3.*) |
| Навигатор | ✅ | DT | — |
| Портной | ✅ | DT | — |
| ДЗ-чекер | ✅ | — | AH, DT |
| Directus (CRM UI) | ✅ | PR | PR (ручные правки) |
| Metabase (BI) | ✅ | PR, AH (RO) | — |
| Ory Kratos | ✅ внешний | PC (sync) | PC (identity) |
| Ory Keto | ✅ внешний | — | — (own store) |
| LMS Aisystant | ✅ внешний | — | AH, DT (DEG) |
| Discourse Club | ✅ внешний | — | AH |
| WakaTime | ✅ внешний | — | AH |
| Stripe / YooKassa / TG Stars | ✅ внешний | — | → Payment Receiver |
| Payment Receiver (CF Worker) | 🔲 WP-246 | — | PR (finance_payments, processed_webhooks) |
| ContentPipeline Worker (CF Worker) | 🟡 WP-155 | CP (jobs, channels, accounts) | CP (publications, events) |
| Langfuse | ✅ внешний | — | — (own store) |
| Nudge / Уведомления | 🟡 | DT, AH | AH |
| Composer MCP (FSM) | 🟡 | AB | AB |
| Epistemic Graph | 🔲 | KN | KN |
| CRM (сервис) | 🔲 | PC, PR | AH |
| Event Bus / Dispatcher | 🔲 | AH | AH |
| AI Training Pipeline | 🔲 | AH, KN | — |
| Team Service | 🔲 | PC | AH |
| uptime-collector | 🔲 | — | HL (доступность + инциденты) |
| Grafana | ✅ внешний | HL (read-only) | — |

---

### 7.3 Поток идентичности и доступа

```mermaid
graph LR
    User([Пользователь])
    Ory([Ory Kratos])
    Keto([Ory Keto])
    GW([gateway-mcp])
    PC[(#1 platform-core)]
    PR[(#5 payment-registry)]

    User -->|"логин / OAuth"| Ory
    Ory  -->|"JWT"| User
    Ory  -->|"webhook: регистрация"| PC
    User -->|"запрос + JWT"| GW
    GW   -->|"проверка JWT"| Ory
    GW   -->|"проверка прав"| Keto
    GW   -->|"проверка подписки"| PC
    PC   -->|"тир + гранты"| GW
    PR   -->|"синхронизация подписок"| PC
```

---

### 7.4 Поток событий → ЦД → персональное руководство

```mermaid
graph TD
    LMS([LMS]) -->|"события"| AH
    Club([Club]) -->|"события"| AH
    Bot([AIST Bot]) -->|"события"| AH
    WakaTime([WakaTime]) -->|"события"| AH
    IWE([IWE / exocortex]) -->|"события"| AH

    AH[(#4 activity-hub)]

    AH -->|"история обучения"| Profiler([Профайлер])
    Profiler -->|"показатели развития"| DT[(#2 digital-twin)]

    DT -->|"профиль"| Tailor([Портной])
    DT -->|"профиль"| Navigator([Навигатор])
    DT -->|"контекст"| Bot
```

> Степень DEG назначается вручную методсоветом МИМ — не через поток событий.

---

### 7.5 Связи между базами данных

```mermaid
graph LR
    PC[(#1 platform-core)]
    DT[(#2 digital-twin)]
    KN[(#3 knowledge)]
    AH[(#4 activity-hub)]
    PR[(#5 payment-registry)]
    AB[(#6 aist-bot)]
    MB[(#7 metabase)]
    HL[(#8 health)]
    CP[(#9 content-pipeline)]

    AH -- "показатели развития" --> DT
    AH -- "связка chat_id → ory_id" --> PC
    PR -- "синхронизация подписок" --> PC
    AB -- "чтение профиля" --> DT
    KN -- "освоение концептов" --> DT
    AB -- "UI конвейера публикаций" --> CP
    CP -- "событие публикации" --> AH
    PR -. "финансы (RLS)" .-> MB
    AH -. "события (без PII)" .-> MB
    HL -. "доступность (только чтение)" .-> MB
```

> Сплошная = запись. Пунктир = чтение (RO).

</details>

---

<details>
<summary><b>8. Кто читает / кто пишет</b></summary>

| База | Writers | Readers |
|------|---------|---------|
| #1 platform-core | Бот (`aist_me_bot_writer`): identity sync + OAuth flows + триал, `sync-subscriptions.sh` cron (из #5 → SUBSCRIPTION_GRANTS), Directus (ручные гранты), миграции (PRODUCTS, MENTOR_ASSIGNMENTS) | gateway-mcp (`gateway_reader`): авторизация каждого запроса, Metabase (`metabase_reader`), activity-hub (identity resolver) |
| #2 digital-twin | Бот dt_sync (`aist_bot_writer`): `2_collected`, Профайлер R28: `3_derived`, knowledge-mcp: LEARNER_CONCEPT_MASTERY (WP-208 TBD), LMS (DEG вручную методсовет МИМ) | dt-mcp (профиль), tailor-mcp (`1_declarative`+`3_derived`), knowledge-mcp (learner_progress, RLS), *бот /twin через gateway→dt-mcp* |
| #3 knowledge | knowledge-mcp ingest (платформенный и персональный контент), gateway-mcp: GITHUB_INSTALLATIONS + USER_SOURCES (GitHub App OAuth, RLS) | knowledge-mcp search, *бот через gateway→knowledge-mcp*, gateway-mcp (список подключённых репо) |
| #4 activity-hub | collectors: LMS + Club + WakaTime + IWE/exocortex + Bot (RAW_EVENTS), transform-worker (RAW→USER→LEARNING) | transform-worker, Профайлер R28 (LEARNING_HISTORY → IND.3.*), Бот dt_sync (USER_EVENTS → ЦД), *Навигатор/Портной: LEARNING_HISTORY*, Metabase (RO, без PII) |
| #5 payment-registry | **Payment Receiver** CF Worker (`payment_receiver_writer`: INSERT finance_payments + processed_webhooks, WP-246), `incremental-sync.sh` cron (стадия 2: только сверка), бот (seminar/workshop записи) | Бот: has_seminar_access/has_access_to_chat, Metabase (`metabase_reader` + RLS, только агрегаты), `sync-subscriptions.sh` cron (FINANCE_PAYMENTS → SUBSCRIPTION_GRANTS в #1) |
| #6 aist-bot | только AIST Bot (44 таблицы: FSM, тренажёры, фид, марафоны, токены, публикации, оплаты, наблюдаемость). ⚠️ 10 таблиц — кандидаты на миграцию: 3→activity-hub, 3→payment-registry, 3→platform-core/#8 health | AIST Bot; Composer MCP (состояние FSM) |
| #7 metabase | Metabase internal (служебные таблицы) | Metabase UI, дашборды читают #5 и #4 напрямую (не через #7) |
| #8 health | uptime-collector cron (GHA, UPTIME_CHECKS + INCIDENTS), AIST Bot error_handler (ERROR_LOGS, REQUEST_TRACES, PENDING_FIXES) | Grafana (RO, дашборды здоровья), Бот (error_classifier, autofix), алерты → TG bot |

</details>

---

<details>
<summary><b>9. Пояснения</b></summary>

### USER_INTEGRATIONS vs GITHUB_CONNECTIONS

Обе таблицы хранят GitHub OAuth-токен одного пользователя — намеренный **dual-write**.

| | GITHUB_CONNECTIONS (platform-core) | USER_INTEGRATIONS (platform-core) |
|---|---|---|
| **Назначение** | Конфигурация публикации: target_repo, notes_path, branches | OAuth-конфиг для коллекторов: WakaTime, IWE Adapter |
| **Потребитель** | Бот-издатель заметок | activity-hub collectors |

`WAKATIME` — только в `USER_INTEGRATIONS`. `GOOGLE_CALENDAR` — только в `GOOGLE_CALENDAR_CONNECTIONS`.

---

### ORY_TOKENS и DT_TOKENS в aist-bot

Это **персистентность бот-сессий**, а не кеш платформы:
- Ключ — `chat_id` (Telegram): бот работает в Telegram-контексте
- Хранятся для переживания редеплоев бота
- `ory_tokens` — токены для вызова Gateway MCP (Ory OAuth)
- `dt_tokens` — токены для вызова DT API
- Токены **зашифрованы** (Fernet, WP-234), в открытом виде в БД не хранятся

---

### LEARNING_HISTORY vs LEARNER_CONCEPT_MASTERY

| | LEARNING_HISTORY (activity-hub) | LEARNER_CONCEPT_MASTERY (digital-twin) |
|---|---|---|
| **Что хранит** | Факты: "прошёл мем X, score 0.8" | Агрегат: "освоен концепт ZP.001 на 85%" |
| **Кто пишет** | transform-worker из USER_EVENTS | profiler (читает LEARNING_HISTORY через API) |

Поток: RAW_EVENTS → USER_EVENTS → LEARNING_HISTORY → profiler API → LEARNER_CONCEPT_MASTERY.
Квалификация "Ученик" и уровни до неё — автоматически из mastery. Выше — методсовет МИМ (ручное).

---

### Кеш авторизации gateway-mcp

`checkSubscription()` в gateway-mcp не обращается к Neon на каждый запрос — результат кешируется в **Cloudflare KV** с TTL 5 мин (WP-235). JWKS также кешируется in-memory в Workers isolate.

Порядок проверок в gateway (до фанаута):
1. `verifyJWT` — проверка подписи токена (JWKS, кеш in-memory)
2. `checkSubscription` — проверка активного гранта в `SUBSCRIPTION_GRANTS` (KV кеш TTL 5 мин)
3. Только при успехе обоих → фанаут в downstream MCP

Отказ на шаге 1 или 2 → немедленный 401/403, downstream не вызывается.

</details>

---

<details>
<summary><b>10. Справочник таблиц</b></summary>

> **Статус:** ✅ Существует в `platform` (единая база, WP-232) | ⚠️ Перенести при разделении | 🆕 Создать
>
> **Writers / Readers** — кто пишет и читает каждую таблицу (Ф2 WP-228, 16 апр 2026). Роль в скобках = DB role. Курсив = непрямой доступ (через MCP/API).

### #1 platform-core

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| USER_IDENTITIES | Маппинг ory_id ↔ telegram_id ↔ lms_id. Только то, чего Ory не знает. | Бот (`aist_me_bot_writer`): OAuth callback + LMS sync | gateway-mcp (`gateway_reader`), activity-hub (identity resolver) | ✅ | `public.user_identities`, WP-232 |
| SUBSCRIPTION_GRANTS | Реестр активных прав подписки. Gateway для всех сервисов. | sync-subscriptions.sh cron (`aist_me_bot_writer`), бот: триал через /start, Directus: ручные гранты | gateway-mcp (`gateway_reader`): проверка на каждый запрос, Metabase (`metabase_reader`) | ✅ | `public.subscription_grants`, WP-231 |
| GITHUB_CONNECTIONS | GitHub OAuth + конфиг публикации (репо, ветки). | Бот (`aist_me_bot_writer`): OAuth flow | Бот: чтение токенов для публикации | ✅ | `public.github_connections`, WP-187 |
| GOOGLE_CALENDAR_CONNECTIONS | Google Calendar OAuth для бота. | Бот (`aist_me_bot_writer`): Google OAuth callback | Бот: чтение токенов для calendar sync | ✅ | `public.google_calendar_connections`, WP-232 |
| USER_INTEGRATIONS | OAuth-конфиг для activity-hub collectors (GitHub, WakaTime). | Бот (`aist_me_bot_writer`): OAuth callbacks | Бот, activity-hub: чтение токенов для API | ⚠️ Перенести | Сейчас в `development.user_integrations` |
| BACKEND_REGISTRY | Реестр персональных MCP-бэкендов пользователей. | gateway-mcp: регистрация BYOB (WP-189, TBD) | gateway-mcp: fan-out маршрутизация | ✅ | `knowledge.backend_registry`, WP-187/189 |
| TIER_EVENTS | Лог переходов тиров (T0→T1 при регистрации и т.д.). Платформенный. | Бот (`aist_me_bot_writer`): core/tier_detector, fire-and-forget | Metabase (аналитика) | 🆕 Перенести из aist-bot | Сейчас в `development.tier_events` (aist-bot) |
| PRODUCTS | Единый каталог продуктов: подписки, программы, семинары. PK = code. | Миграции (`neondb_owner`): seed, Directus: CMS | Бот: метаданные семинаров/программ, Directus | ✅ | `public.products`, WP-228 |
| MENTOR_ASSIGNMENTS | Привязка наставников к продуктам по product_code. | Миграции, Directus | Бот: lookup наставников | ✅ | `public.mentor_assignments`, WP-228 |

### #2 digital-twin

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| DIGITAL_TWINS | Цифровой двойник: 3 слоя (базовые / вовлечённость / производные). | Бот dt_sync (`aist_bot_writer`): `2_collected` (cron 04:30), Profiler R28: `3_derived` (standalone) | dt-mcp: профиль по API, tailor-mcp: `1_declarative`+`3_derived`, *бот /twin через gateway→dt-mcp* | ✅ | `public.digital_twins`, WP-227 |
| POINT_TRANSACTIONS | Лог начислений/списаний баллов активности. Append-only. | Бот (`aist_bot_writer`): calculate_points (WP-121 Ф2, TBD) | gateway_reader: баланс (TBD) | ✅ | `points.point_transactions`, WP-121 |
| LEARNER_CONCEPT_MASTERY | Степень освоения концептов (0.0–1.0). Основа для квалификации "Ученик". Реализация 🔗-связи «Созидатель освоил Концепт» (DP.METHOD.040 §4: связь с атрибутами `mastery_level`, `last_reviewed_at`). | knowledge-mcp: analyze_verbalization (RLS, WP-208 TBD) | knowledge-mcp: learner_progress (RLS по user_id) | ✅ (физ.реализация 🔗) | `concept_graph.learner_concept_mastery`, WP-151 |

### #3 knowledge

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| DOCUMENTS | Документы + векторные эмбеддинги для semantic search. | knowledge-mcp ingest + /reindex webhook (RLS) | knowledge-mcp: hybrid search, *бот через gateway→knowledge-mcp* | ✅ | `knowledge.documents`, knowledge-mcp |
| RETRIEVAL_FEEDBACK | Обратная связь по релевантности документов. | knowledge-mcp: recordFeedback tool (RLS) | knowledge-mcp: feedback_stats | ⚠️ → activity-hub (событие retrieval feedback) | `knowledge.retrieval_feedback`, knowledge-mcp |
| CONCEPTS | Граф концептов платформы (ZP, FPF, Pack, курсы). | knowledge-mcp ingest-concepts.ts (без RLS, платформенные) | knowledge-mcp: analyze_verbalization, graph_stats, *бот через gateway* | ✅ | `concept_graph.concepts`, WP-170 |
| CONCEPT_EDGES | Рёбра графа: prerequisite, related, part_of, contradicts. | knowledge-mcp ingest-concepts.ts | knowledge-mcp: graph_stats, edge coverage | ✅ | `concept_graph.concept_edges` |
| CONCEPT_MISCONCEPTIONS | Типовые заблуждения по концептам. | knowledge-mcp ingest-concepts.ts (из CAT.001) | knowledge-mcp: analyze_verbalization (LLM-detection) | ✅ | `concept_graph.concept_misconceptions` |
| GITHUB_INSTALLATIONS | GitHub App installations для индексации репо. | gateway-mcp: GitHub App OAuth (WP-187, RLS) | gateway-mcp: список подключённых репо | ✅ | `knowledge.github_installations`, WP-187 |
| USER_SOURCES | Источники индексации (GitHub репо, активные/нет). | knowledge-mcp миграция 004 + gateway-mcp webhook (RLS) | knowledge-mcp: resolve user_id при ingest, gateway-mcp: UI источников | ✅ | `knowledge.user_sources`, knowledge-mcp |

### #4 activity-hub

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| RAW_EVENTS | Bronze: сырые события, партиционированы по (source, fetched_at). TTL 30д. | activity-hub adapters (`aist_bot_writer`): write_raw, ON CONFLICT DO NOTHING | activity-hub transform-worker: _fetch_pending, integrity checks | ✅ | `development.raw_events`, WP-109 Ф8.1 |
| USER_EVENTS | Silver: нормализованные события с атрибуцией пользователя. TTL 90д. | activity-hub: (1) hub.py ingest_event direct INSERT, (2) transform-worker upsert, (3) ingest_batch bulk | activity-hub: rate_limit check, бот dt_sync: агрегация для ЦД | ✅ | `development.user_events`, WP-109 |
| LEARNING_HISTORY | Gold: факты обучения. Читается profiler. Archive в S3 после 5 лет. | Бот: DB-триггер на user_events INSERT (миграция 007), backfill (миграция 010) | Бот dt_sync: BKT-расчёты, Навигатор: 7-дневное окно, *Портной: depths_by_direction* | ⚠️ Перенести | Сейчас в aist_bot (миграция 007) |
| IDENTITY_MAP | chat_id → ory_id; NULL до OAuth. | activity-hub runner.py: populate_bot/csv/lms_identity | activity-hub hub.py: resolve_user_uuid, бот dt_sync: LMS qualification mapping | ✅ | `development.identity_map`, WP-109 |
| SYNC_LOG | Журнал запусков коллекторов. TTL 30д. | activity-hub runner.py: log_sync после каждого запуска | activity-hub: мониторинг, Metabase (TBD) | ✅ | `development.sync_log`, WP-109 |
| QUARANTINED_EVENTS | Карантин для невалидных событий. | activity-hub: hub.py _quarantine (validation fail), transform-worker (parse fail) | Ручной разбор (write-only sink) | ✅ | `development.quarantined_events`, WP-109 |
| CONVERSION_EVENTS | События конверсионной воронки (chat_id, trigger_type C1-C7, milestone, action shown/clicked/dismissed). Чистое событие воронки, без payment_id / amount. | Бот: core/scheduler, conversion.py | Бот: cooldown check, Metabase (RO аналитика) | ✅ (принято 20 апр: → #4) | Решение 20 апр 2026 (Р3): по смыслу событие воронки, не платёж |

### #5 payment-registry

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| FINANCE_PAYMENTS | Реестр всех транзакций. Permanent. `raw_payload JSONB` (WP-246). | Payment Receiver CF Worker (`payment_receiver_writer`), incremental-sync.sh cron (`aist_me_bot_writer`), бот showcase handler | Бот: has_seminar_access/has_access_to_chat, Metabase (`metabase_reader`), sync-subscriptions.sh: → SUBSCRIPTION_GRANTS | ✅ | `public.finance_payments`, WP-183 |
| PROCESSED_WEBHOOKS | Idempotency-дедупликация webhook'ов. PK = event_id. TTL >72h. | Payment Receiver (`payment_receiver_writer`): isDuplicate + markProcessed | Payment Receiver: проверка дубликатов | 🆕 Создать | `public.processed_webhooks`, WP-246 |
| FINANCE_PAYMENTS_AUDIT_LOG | Append-only лог изменений статуса. 7 лет. | (WP-237, TBD) | (WP-237, TBD) | 🆕 Создать | WP-237 |
| PAYMENTS_SYNC_STATE | Ватермарк импорта из Aisystant. | incremental-sync.sh (`aist_me_bot_writer`) | incremental-sync.sh, Metabase: sync health | ✅ | `public.finance_payments_sync_state`, WP-183 |
| SUBSCRIPTION_GRANTS_SYNC_STATE | Ватермарк синхронизации грантов. | sync-subscriptions.sh (`aist_me_bot_writer`) | sync-subscriptions.sh: read boundary | ✅ | WP-231 |
| IMPORT_STAGING_PAYMENT | Landing zone импорта платежей из Aisystant. | incremental-sync.sh: TRUNCATE + COPY | incremental-sync.sql: transform → finance_payments | ✅ | WP-183 (миграция 005) |
| IMPORT_STAGING_CHARGEOFF | Landing zone списаний. | incremental-sync.sh: TRUNCATE + COPY | incremental-sync.sql: transform → UPDATE linked | ✅ | WP-183 (миграция 005) |
| WORKSHOP_PAYMENTS | Оплаты воркшопов через бота. | Бот: db/queries/workshop, handlers/workshop | Бот: db/queries/workshop | ⚠️ Перенести из aist-bot | Сейчас в aist_bot |
| SEMINAR_PAYMENTS | Оплаты семинаров через бота. | Бот: handlers/payments, db/queries/showcase | Бот: db/queries/showcase | ⚠️ Перенести из aist-bot | Сейчас в aist_bot |
| AUTOPAY | Договор автосписания с пользователем (payment_method_id YooKassa). | payment-receiver (`payment_receiver_writer`, WP-246) | autopay-executor, Бот (проверка статуса) | ✅ (план WP-246 Ф10-Ф16) | WP-246 Стадия 3 |
| AUTOPAY_DATA | Состояние/история автосписаний (попытки, ответы YooKassa, даты). | autopay-executor CF Worker | Бот, Metabase (RO, без payment_method_id) | ✅ (план WP-246 Ф10-Ф16) | WP-246 Стадия 3 |

### #6 aist-bot

> **WP-228 Ф2 (16 апр):** расширен с 11 до 44 таблиц. Таблицы-кандидаты на миграцию помечены ⚠️ с целевой базой.

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| **Ядро бота (FSM, сессии, идентичность)** | | | | | |
| USERS | Локальные пользователи бота (telegram_id, ory_id, роли). | handlers/onboarding, core/ory_register | core/\*, handlers/\*, db/queries/\* | ✅ | Первая миграция |
| USER_STATE | FSM-состояние бота + счётчик активных дней, триал. ⚠️ Колонки tier/mentor_tier убрать после переноса. | handlers/onboarding, states/\*, core/scheduler, core/machine | handlers/\*, states/\*, core/machine | ✅ | `development.user_state` |
| FSM_STATES | Хранилище FSM aiogram (персистентность между редеплоями). | core/storage (aiogram) | core/storage | ✅ | aiogram FSM |
| USER_SESSIONS | Сессии пользователей. | db/queries/sessions | db/queries/analytics | ✅ | Миграция бота |
| OAUTH_PENDING_STATES | Ожидающие OAuth state-коды (GitHub, Google). TTL: до завершения flow. | db/queries/oauth_states, handlers/github | db/queries/oauth_states | ✅ | Миграция бота |
| **Обучение (тренажёры, фид, марафоны)** | | | | | |
| TRAINING_PROGRESS | Прогресс тренажёров (мемы, задания). | db/queries/training, states/training/\* | db/queries/training, states/training/\* | ✅ | Миграция бота |
| TRAINING_CHILDREN | Детские учебные треки (физ.объект: конкретный Детский-трек). | db/queries/training | db/queries/training | ✅ физ.объект «Детский-трек» (добавлен в §7.0.2 #6) | Миграция бота |
| TRAINING_ATTEMPTS | Попытки прохождения (score, ответы). | db/queries/training, states/training/\* | db/queries/training | ✅ | Миграция бота |
| TRAINING_SETTINGS | Пользовательские настройки тренажёров. | db/queries/training, handlers/settings | db/queries/training | 🔗 связь «Созидатель ↔ Настройка-трека» | Миграция бота |
| FEED_SESSIONS | Сессии ленты обучения. | db/queries/feed, states/feed/\* | db/queries/feed, db/queries/answers | ✅ | Миграция бота |
| FEED_WEEKS | Недельные циклы ленты. | db/queries/feed, states/feed/\* | db/queries/feed, db/queries/answers | ✅ | Миграция бота |
| MARATHON_CONTENT | Контент марафонов (уроки, задания). | db/queries/marathon, core/scheduler | db/queries/marathon, states/lesson | ✅ | Миграция бота |
| ANSWERS | Ответы пользователей на задания. | db/queries/answers, states/task | db/queries/answers, db/queries/profile | ✅ | Миграция бота |
| ASSESSMENTS | Оценки (самооценка, внешняя). | db/queries/assessment, states/assessment/\* | db/queries/assessment, db/queries/dev_stats | ✅ | Миграция бота |
| CONTENT_CACHE | Кеш контента LMS (уроки, задания). TTL: по scheduler. | db/queries/cache, core/scheduler | db/queries/cache, states/lesson, states/task | ✅ | Миграция бота |
| **Коммуникация и фидбек** | | | | | |
| QA_HISTORY | История вопросов/ответов. TTL 180д. | db/queries/qa, states/consultation | db/queries/analytics, handlers/twin | ✅ | `public.qa_history`, WP-132 |
| NOTIFICATION_LOG | Журнал уведомлений с idempotency_key. TTL 30д. | db/queries/notifications, core/scheduler | db/queries/notifications | ✅ | `public.notification_log`, WP-232 |
| FEEDBACK_TRIAGE | Фидбек из бота (source='bot'). | core/feedback_triage, handlers/feedback | core/feedback_triage, db/queries/feedback | ✅ | Миграция 008 |
| FEEDBACK_REPORTS | Отчёты по фидбеку (агрегированные). | db/queries/feedback, handlers/feedback | db/queries/feedback | ⚠️ → activity-hub (событие feedback) | Миграция бота |
| REMINDERS | Расписание напоминаний. | core/scheduler | core/scheduler | ✅ | Миграция бота |
| **Подписки и доступ** | | | | | |
| BOT_SUBSCRIPTIONS | 🔵 Проекция из #1 platform-core (SUBSCRIPTION_GRANTS) — read-cache для бота (Stars-подписки, бот-уровень). | db/queries/subscription, handlers/subscription | db/queries/subscription, core/tier_detector | 🔵 Проекция (source-of-truth = #1) | Проекция/кеш из `public.subscription_grants` |
| SERVICE_USAGE | Счётчик использования сервисов бота. | db/queries/activity | db/queries/analytics, db/queries/dev_stats | ⚠️ → activity-hub (событие usage tracking) | Миграция 003 |
| USER_ACCESS | Временные права (выданные ботом, с expiry). | (не найден активный writer) | (не найден активный reader) | 🔗 связь «Созидатель ↔ Ресурс: права доступа» | Миграция 002 |
| COMMUNITY_MEMBERS | Участники Telegram-сообщества. | db/queries/workshop, handlers/workshop | db/queries/workshop | ✅ | Миграция 009 |
| **Токены и интеграции** | | | | | |
| ORY_TOKENS | Ory OAuth-токены бота. Зашифрованы Fernet. | db/queries/ory_tokens, handlers/ory_register | db/queries/ory_tokens | ✅ | `public.ory_tokens`, WP-209. ⚠️ plaintext → WP-234 |
| DT_TOKENS | DT OAuth-токены бота. Зашифрованы Fernet. | db/queries/dt_tokens, handlers/connect | db/queries/dt_tokens, core/scheduler | ✅ | `public.dt_tokens`, WP-82. ⚠️ plaintext → WP-234 |
| **Публикации и каналы** | | | | | |
| CHANNEL_MONITORS | Мониторинг Telegram-каналов. | db/queries/channels, handlers/channels | db/queries/channels | ✅ | Миграция бота |
| CHANNEL_MENTIONS_LOG | Лог упоминаний в каналах. | db/queries/channels, handlers/channels | db/queries/channels | ✅ | Миграция бота |
| **Платежи (бот-уровень)** | | | | | |
| WORKSHOP_PAYMENTS | Оплаты воркшопов через бота. | db/queries/workshop, handlers/workshop | db/queries/workshop | ⚠️ → payment-registry | Миграция бота |
| SEMINAR_PAYMENTS | Оплаты семинаров через бота. | handlers/payments, db/queries/showcase | db/queries/showcase | ⚠️ → payment-registry | Миграция бота |
| CONVERSION_EVENTS | События конверсионной воронки. | db/queries/conversion, core/scheduler | db/queries/conversion, db/queries/analytics | ⚠️ → activity-hub (принято 20 апр: Р3) | Миграция бота |
| ~~SEMINARS~~ | ~~Каталог семинаров~~ → заменена PRODUCTS в platform-core. | — | — | ❌ Удалена | Заменена `public.products` |
| **Наблюдаемость (бот-уровень)** | | | | | |
| ACTIVITY_LOG | Лог активности пользователей в боте. | db/queries/activity, core/scheduler | db/queries/activity, db/queries/analytics | ✅ | Миграция бота |
| ERROR_LOGS | Ошибки бота (категория, severity). TTL 180д. | core/error_handler | core/error_classifier, db/queries/analytics | ✅ | Миграция бота. ⚠️ Копия → #8 health |
| REQUEST_TRACES | Трейсы запросов бота. TTL 30д. | core/tracing | db/queries/analytics | ✅ | Миграция бота. ⚠️ Копия → #8 health |
| **Платформенные таблицы (в aist-bot DB, target: другая база)** | | | | | |
| TIER_EVENTS | Лог переходов тиров. | core/tier_detector | core/tier_detector, Metabase | ⚠️ → platform-core | `development.tier_events` |
| LEARNING_HISTORY | Gold-факты обучения (DB-триггер на user_events). | DB-триггер (миграция 007), backfill (миграция 010) | Бот dt_sync: BKT, Навигатор: 7д окно | ⚠️ → activity-hub | Миграция 007 |
| USER_EVENTS | Нормализованные события (дублирует #4). | db/queries/events, core/scheduler | db/queries/dt_sync, db/queries/events | ⚠️ → activity-hub | Бот-копия, target: #4 |

### #7 metabase

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| METABASE_COLLECTIONS | Папки дашбордов. | Metabase internal | Metabase UI | ✅ | Управляется Metabase (~171 таблица) |
| METABASE_DASHBOARDS | Дашборды (финансы, события). | Metabase internal | Metabase UI, дашборды читают #5 и #4 напрямую | ✅ | WP-183 (2 дашборда) |
| METABASE_CARDS | Questions (8 штук). | Metabase internal | Metabase UI | ✅ | WP-183 |
| METABASE_USERS | Пользователи Metabase (не Ory). | Metabase internal | Metabase internal | ✅ | Управляется Metabase |

### #8 health

| Таблица | Назначение | Writers | Readers | Статус | Источник |
|---------|-----------|---------|---------|--------|----------|
| SERVICE_REGISTRY | Реестр сервисов для мониторинга (name, url, check_type). | uptime-collector (GHA cron) | Grafana (RO) | 🆕 Создать | WP-244 |
| UPTIME_CHECKS | Результаты пингов (latency_ms, status_code, is_up). TTL 90d. | uptime-collector (GHA cron) | Grafana (RO), алерты → TG | 🆕 Создать | WP-244 |
| UPTIME_INCIDENTS | Агрегированные инциденты (started_at, resolved_at, severity). Permanent. | uptime-collector (агрегация) | Grafana (RO), алерты → TG | 🆕 Создать | WP-244 |
| ANTHROPIC_STATUS_SNAPSHOTS | Снапшоты status.anthropic.com API по компонентам. TTL 90d. | uptime-collector (GHA cron) | Grafana (RO) | 🆕 Создать | WP-244 |
| ERROR_LOGS | Структурированные ошибки сервисов (категория, severity, дедупликация). TTL 180d. | Бот error_handler (сейчас в aist_bot), будущее: все сервисы | Grafana (RO), core/error_classifier | 🆕 Перенести | WP-45 (сейчас в `platform`) |
| REQUEST_TRACES | Трейсы запросов (OTel trace_id, span, latency). TTL 30d. | Бот core/tracing (сейчас в aist_bot), будущее: все сервисы | Grafana (RO) | 🆕 Перенести | WP-45 (сейчас в `platform`) |
| PENDING_FIXES | Очередь auto-fix (диагноз Claude, статус approve/reject). TTL 90d. | Бот core/autofix | Бот db/queries/autofix, Grafana | ✅ Перенесено (WP-244) | WP-45 → WP-244 |

</details>

---

<details>
<summary><b>11. Архитектурные решения</b></summary>

> Принятые решения по проблемам, выявленным при ArchGate-ревью. Все изменения уже отражены в ERD выше (раздел 5) и справочнике таблиц (раздел 8).

---

### WP-234 — Шифрование OAuth-токенов (aist-bot)

**Проблема:** `access_token` и `refresh_token` в ORY_TOKENS, DT_TOKENS, GITHUB_CONNECTIONS, USER_INTEGRATIONS хранились как TEXT. При компрометации базы — все OAuth-сессии утекают. Подтверждено в коде: `cryptography` отсутствует в `requirements.txt`.

**Решение:** Python Fernet (AES-128-CBC + HMAC-SHA256). Тип колонок: TEXT → BYTEA. Добавлена колонка `token_nonce TEXT`. Ключ `FERNET_KEY` из env (Railway secrets). Scheduler проверяет `updated_at < now() - 10 min` → принудительный refresh.

**Трудозатраты:** ~16h. Приоритет: критический.

---

### WP-235 — Кеш авторизации (gateway-mcp)

**Проблема:** `checkSubscription()` в `gateway-mcp/src/index.ts` делает SELECT в Neon на каждый запрос. Подтверждено в коде. При 10k пользователей = тысячи DB-запросов в минуту.

**Решение:** Cloudflare KV с TTL 5 мин. In-memory Map не подходит (Workers stateless, разные isolates → cache miss 80%+). При отзыве подписки — явная инвалидация `kv.delete(key)`.

**Отклонённая альтернатива: подписка в JWT.** Предложение — положить `subscription: active` в JWT и убрать SELECT совсем. Отклонено по двум причинам: (1) JWT выдаёт Ory Kratos, который не знает о биллинге — добавление бизнес-данных нарушает разделение ответственности (Ory = идентичность, не биллинг); (2) при отзыве подписки JWT остаётся валидным до истечения TTL — задержка такая же как у KV cache, но без возможности явной инвалидации. KV cache даёт тот же TTL 5 мин с возможностью немедленного отзыва через `kv.delete(key)`.

**Трудозатраты:** 8h. Приоритет: высокий.

---

### WP-236 — Correlation ID в activity-hub

**Проблема:** RAW_EVENTS, USER_EVENTS, LEARNING_HISTORY не имели `trace_id`. Нельзя проследить путь события bronze→silver→gold. Подтверждено в миграциях.

**Решение:** OpenTelemetry trace_id (CHAR(32), 128-bit hex). Генерируется при ingestion в RAW_EVENTS, propagated transform-worker в USER_EVENTS и LEARNING_HISTORY. Интегрируется с Langfuse.

**Трудозатраты:** ~20h. Приоритет: высокий.

---

### WP-237 — Audit trail для FINANCE_PAYMENTS

**Проблема:** Нет истории изменений статуса платежа. Нельзя ответить кто и когда изменил статус.

**Решение:** Новая таблица `FINANCE_PAYMENTS_AUDIT_LOG` + Postgres trigger `tr_log_payment_changes`. Срабатывает на UPDATE, пишет в лог автоматически. `changed_by` = имя сервиса. Retention: 7 лет (compliance).

**Трудозатраты:** ~14h. Приоритет: высокий.

---

### WP-238 — RLS для Metabase

**Проблема:** Metabase читала FINANCE_PAYMENTS без ограничений. При компрометации — все финансовые данные открыты.

**Решение:** Роль `metabase_reader` + агрегированные views без PII. `REVOKE ALL ON FINANCE_PAYMENTS FROM metabase_reader`. RLS policy `USING (FALSE)` блокирует прямой доступ к таблице.

**Трудозатраты:** ~8h. Приоритет: средний.

---

### WP-239 — SSRF защита BACKEND_REGISTRY

**Проблема:** Пользователи регистрируют кастомные MCP-URL без валидации → SSRF риск.

**Решение:** Валидация при записи: только `https://`, запрет RFC1918 (127.x, 10.x, 172.16-31.x, 192.168.x, 169.254.x), запрет портов 22/23/25/135/139/445/3389, максимум 512 символов. Отражено в note поля `backend_url` в ERD.

**Трудозатраты:** ~10h. Приоритет: средний.

---

### WP-240 — Retention policy

**Проблема:** Таблицы растут бесконечно. GDPR требует права на удаление данных.

**Решение:** pg_partman DROP для RAW_EVENTS (TTL 30д). Daily cron для остальных. Retention по таблицам:

| Таблица | TTL | Механизм |
|---------|-----|----------|
| RAW_EVENTS | 30 дней | pg_partman DROP |
| USER_EVENTS | 90 дней | DELETE cron |
| LEARNING_HISTORY | Постоянно | Archive S3 после 5 лет |
| FINANCE_PAYMENTS | Постоянно | Annual archive |
| FINANCE_PAYMENTS_AUDIT_LOG | 7 лет | Archive (compliance) |
| QA_HISTORY | 180 дней | DELETE cron |
| PROCESSED_WEBHOOKS | 7 дней | DELETE cron (TTL >72h Stripe retry, запас до 7д) |
| NOTIFICATION_LOG | 30 дней | DELETE cron |
| SYNC_LOG | 30 дней | DELETE cron |
| USER_STATE (неактивные) | 90 дней | soft-delete (is_inactive=true) |

**Трудозатраты:** ~16h. Приоритет: средний.

---

### WP-244 — DB #8 health: операционный мониторинг платформы

**Проблема:** Нет систематического учёта здоровья сервисов. Инциденты обнаруживаются случайно или с задержкой (WP-183: 4.5h без детекции). Таблицы WP-45 (`error_logs`, `request_traces`, `pending_fixes`) не размещены ни в одну целевую базу — «бесхозные» в целевой архитектуре. Нет корреляции деградации наших сервисов с Anthropic API.

**Решение:** Отдельная DB #8 `health` — cross-cutting операционные данные, не принадлежащие ни одному продуктовому сервису. ArchGate пройден: варианты obs.* в platform-core (❌ нарушает П1, смешивает с identity-данными) и obs.* в aist-bot (❌ бот = клиент, не хранилище платформенных данных) отклонены. DB #8 изолирует credentials: компрометация коллектора не открывает PII.

**Таблицы:** SERVICE_REGISTRY, UPTIME_CHECKS, UPTIME_INCIDENTS, ANTHROPIC_STATUS_SNAPSHOTS (новые) + ERROR_LOGS, REQUEST_TRACES, PENDING_FIXES (перенос из WP-45).

**Writer:** uptime-collector (GHA cron, каждые 5 мин) + AIST Bot error_handler. **Reader:** Grafana (RO).

**Трудозатраты:** ~20h. Приоритет: средний.

---

### WP-246 — Payment Receiver (прямые платежи)

**Проблема:** Все платежи проходят через Aisystant (pull-sync каждые 5-15 мин). Aisystant = единая точка отказа; задержка 10-40 мин от оплаты до начисления подписки. При недоступности Aisystant платежи теряются в окне.

**Решение:** Payment Receiver — Cloudflare Worker, принимает webhook от провайдеров (YooKassa, Stripe, Paybox, Tilda, TG Stars) и пишет напрямую в Neon. 3-стадийная модель перехода (Strangler Fig):
- **Стадия 0 (текущая):** ch1-5 через Aisystant pull-sync
- **Стадия 1 (переходная):** Payment Receiver принимает webhook → INSERT в Neon + forward в Aisystant (Дима продолжает работать)
- **Стадия 2 (целевая):** Aisystant читает из Neon (SSOT). Forward-proxy убирается, incremental-sync → только reconciliation

**Новые артефакты в Neon:** таблица `PROCESSED_WEBHOOKS` (idempotency), колонка `raw_payload JSONB` в FINANCE_PAYMENTS (аудит webhook), роль `payment_receiver_writer` (INSERT finance_payments + processed_webhooks).

**Волны миграции:** ch6 (YooKassa-бот) → ch3 (Stripe) → ch1,2,4,5 (YooKassa-LMS).

**Трудозатраты:** ~33h. Приоритет: высокий. ArchGate: 6✅/1⚠️. Концепция: DP.CONCEPT.004. SC: DP.SC.120.

---

### WP-241 — Backup / DR

**Проблема:** Не определены RPO/RTO. Neon PITR 7 дней недостаточно для payment-registry (compliance).

**Решение:** Neon PITR per-database + pg_dump в S3 через unpooled endpoint.

| База | RPO | RTO | Механизм |
|------|-----|-----|----------|
| platform-core | 1h | 15 мин | PITR 7d + S3 daily |
| payment-registry | 15 мин | 30 мин | PITR **30d** (upgrade) + S3 каждые 6h |
| digital-twin | 1h | 30 мин | PITR 7d + S3 daily |
| activity-hub | 24h | 1h | PITR 7d (события воспроизводимы) |
| knowledge | 7 дней | 2h | S3 weekly (знания в git) |
| aist-bot | 24h | 2h | PITR 7d |

**Трудозатраты:** ~18h. Приоритет: средний.

</details>

---

<details open>
<summary><b>12. Сводная таблица РП</b></summary>

> Все перечисленные РП **ещё не выполнены** — это план работ, необходимый для перехода к целевой архитектуре. Текущее состояние: одна база `platform` (WP-232). Разделение и реализация решений ниже — следующие шаги.

| РП | Что нужно сделать | Оценка | Приоритет |
|----|------------------|--------|-----------|
| WP-234 | Зашифровать OAuth-токены в #6 aist-bot (Fernet, BYTEA) | 16h | критический |
| WP-235 | Добавить кеш авторизации в gateway-mcp (Cloudflare KV, TTL 5 мин) | 8h | высокий |
| WP-236 | Добавить trace_id в #4 activity-hub (RAW→USER→LEARNING, OTel) | 12h | высокий |
| WP-237 | Создать audit trail для #5 payment-registry (таблица + Postgres trigger) | 10h | высокий |
| WP-238 | Настроить RLS для Metabase → #5 payment-registry (роль + views без PII) | 6h | средний |
| WP-239 | Добавить валидацию URL в #1 platform-core BACKEND_REGISTRY (SSRF защита) | 6h | средний |
| WP-240 | Настроить retention policy для всех баз (pg_partman + cron + S3 archive) | 16h | средний |
| WP-241 | Настроить backup / DR (PITR per-database + pg_dump → S3) | 14h | средний |
| WP-244 | Создать DB #8 health: SERVICE_REGISTRY, UPTIME_CHECKS, UPTIME_INCIDENTS, ANTHROPIC_STATUS_SNAPSHOTS + перенести ERROR_LOGS/REQUEST_TRACES/PENDING_FIXES из WP-45 | ~20h | средний |
| WP-246 | Payment Receiver (CF Worker): прямой приём webhook от провайдеров → Neon. 3-стадийный Strangler Fig (pull → push+forward → Neon SSOT). Новая таблица PROCESSED_WEBHOOKS, роль payment_receiver_writer | ~33h | высокий |
| WP-215 | Разделение инфраструктуры мир/Россия: реализовать эту схему в двух инстансах, найти альтернативу Neon для РФ-контура (Neon — US-hosted, GDPR/152-ФЗ), определить стратегию синхронизации | ~40h | высокий |
| | **Итого** | **~181h** | |

**Критический путь:**
1. WP-234 + WP-235 — безопасность и авторизация (параллельно, ~2 нед)
2. WP-236 + WP-237 + WP-238 + WP-239 — данные и compliance (параллельно, ~2 нед)
3. WP-240 + WP-241 — операционная надёжность (~1 нед)
4. WP-215 — региональное разделение (блокирует production для РФ-пользователей)

</details>
