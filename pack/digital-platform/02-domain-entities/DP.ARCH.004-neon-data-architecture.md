---
id: DP.ARCH.004
name: Архитектура данных Neon (Database-per-BoundedContext)
type: domain-entity
status: active
valid_from: 2026-04-22
supersedes: "DP.ARCH.004 v1 (14 апр 2026) — 9 БД, монолитный ЦД"
summary: "12 баз данных Neon по принципу database-per-BoundedContext. Сводная таблица, карта, ERD по каждой БД, связи, потоки, реестр физ.объектов, миграция 9 → 12 БД, верификация по чеклистам SPF.SPEC.005, замечаниям Андрея Д1-Д12 и категориям WP-257."
related:
  specializes: [U.System]
  uses: [DP.SOTA.016, DP.ARCH.005, DP.ARCH.006, DP.ARCH.007, DP.ARCH.001, DP.D.052, DP.METHOD.040]
  supersedes_term: "ЦД (монолитный цифровой двойник) → Персона + Память (Observed/Derived) + Контекст (WP-257)"
decision_source: "Встречи ИТ 8 и 10 (14 апр, 21 апр 2026); ревью Андрея Ф24 (Д1-Д12); целевая карта Ф25 (22 апр); канон Персона/Память/Контекст (WP-257)"
---

# DP.ARCH.004 — Архитектура данных Neon

**Целевое состояние:** 12 баз данных Neon + 1 внешняя система (LMS Aisystant, legacy). Каждая БД = один BoundedContext с единым семантическим writer-owner. Термин «ЦД» (монолитный цифровой двойник) упразднён — на его место пришла трёхслойная модель Персона / Память / Контекст (WP-257). 9 БД предыдущей версии декомпозированы с учётом 12 правок Андрея (Ф24, Д1-Д12) и правил границ SPF.SPEC.005.

**Документ построен так:** сначала — сводная таблица 12 БД (§1), затем карта (§2), потом ERD по каждой БД (§3), связи между БД (§4), потоки (§5), писатели-читатели (§6), что осталось вне Neon (§7), миграция (§8), верификация по трём чеклистам (§9), открытые вопросы (§10).

---

## 1. Сводная таблица 12 БД (целевая карта)

**Источник:** WP-228 Ф25 (22 апр 2026). Каждая строка проходит Step 0 SPF.SPEC.005 (категория WP-257) + B1-B4 (выделение БД) + N1-N5 (именование). Верификация — §9.

<details open>
<summary><b>Полная сводная таблица 12 БД</b></summary>

| # | БД (RU / `en`) | Категория WP-257 | Кто пишет (writer-owner) | Физ.объекты домена | Таблицы (RU / `en`) |
|---|---|---|---|---|---|
| 1 | **Персона** / `persona` | Персона | пользователь через любой интерфейс (VS Code, бот, веб, CLI) + personal-indexer (эмбеддинги личных Git-репо) | учётка (ссылка на Ory), декларация о себе (роли/ценности/цели), предпочтения, захваты, заметки, проекция PACK-personal, проекция DS-my-strategy, история редакций | Учётка / `account`, Предпочтения / `preferences`, Захваты / `captures`, Заметки / `notes`, Коллекция / `collection`, Документ / `document`, Параграф / `paragraph`, Эмбеддинг / `embedding` |
| 2 | **Журнал** / `journal` | Память.Observed | event-коллектор (единая точка записи от бота, веба, VS Code, CLI) | действие пользователя (открытие, чтение, ответ, клик), запись журнала, сессия, контекст действия (агент, артефакт) | Событие / `event`, Сессия / `session`, Контекст-действия / `action_context` |
| 3 | **Платёж** / `payment` | Память.Observed | billing-webhook (ЮKassa, Telegram Stars), админ через Directus | плательщик (юрик/физик, ≠ персона 1:1), транзакция, возврат, метод оплаты, чек | Плательщик / `payer`, Платёж / `payment`, Возврат / `refund`, Метод-оплаты / `payment_method`, Чек / `receipt` |
| 4 | **Подписка** / `subscription` | Память.Observed | subscription-service (создание грантов, продление, отмена, пауза) | тарифный план, грант подписки, автосписание-график, история грантов | План / `plan`, Грант / `grant`, Автосписание / `autopay`, История-грантов / `grant_history` |
| 5 | **Показатели** / `indicators` | Память.Derived | Портной-engine (расчёт baseline, potential, индикаторов, RCS-ступени, проекций) | замер (now, зашумлён), baseline-значение (устойчивое, сглаженное по окну), потенциал (потолок на момент t), индикатор (derived-композит), RCS-ступень текущая (1-5), снапшот Pack-состояния (кэш on-demand) | Замер / `measurement`, Baseline / `baseline`, Потенциал / `potential`, Индикатор / `indicator`, RCS-ступень / `rcs_current`, Снапшот / `snapshot` |
| 6 | **Обучение** / `learning` | Память.Observed + Derived | learning-service, наставники через Directus, scheduler лент/дайджестов | зачисление, прогресс по курсу, рабочая тетрадь, ДЗ, ответ ученика, оценка, проверка наставника, попытка практики, Q&A, марафон, обратная связь, лента-неделя, лента-сессия | Зачисление / `enrollment`, Прогресс / `course_progress`, Рабочая-тетрадь / `workbook`, ДЗ / `assignment`, Ответ / `answer`, Оценка / `assessment`, Проверка-наставника / `mentor_review`, Попытка / `practice_attempt`, Вопрос-ответ / `qa_pair`, Марафон / `marathon`, Обратная-связь / `feedback`, Лента-неделя / `feed_week`, Лента-сессия / `feed_session` |
| 7 | **Знание-платформы** / `knowledge` | Platform-knowledge (проекция) | knowledge-mcp индексатор (эмбеддинги PACK-digital-platform, PACK-MIM, PACK-ecosystem, ZP, FPF, SOTA, Glossary) | коллекция (namespace по источнику), понятие, связь между понятиями, документ, параграф, эмбеддинг, версия индекса | Коллекция / `collection`, Понятие / `concept`, Связь / `relation`, Документ / `document`, Параграф / `paragraph`, Эмбеддинг / `embedding`, Версия-индекса / `index_version` |
| 8 | **Справочник** / `reference` | Catalog/Reference | админ через Directus (редкие правки, source-of-truth = реестры и решения Методсовета) | тариф, ступень ученика (1-5 RCS), уровень квалификации (11-ступенчатая шкала), правило начисления баллов, множитель, константа, программа (ЛР/РР) | Тариф / `tariff`, Ступень-ученика / `rcs_stage`, Уровень-квалификации / `qualification_level`, Правило-начисления / `reward_rule`, Множитель / `multiplier`, Константа / `constant`, Программа / `program` |
| 9 | **Публикации** / `publication` | Память.Observed | content-pipeline (создание → публикация → каналы) | черновик, статья, пост, канал публикации, событие публикации, UTM-метка, аналитика охвата | Черновик / `draft`, Статья / `article`, Пост / `post`, Канал / `channel`, Событие-публикации / `publication_event`, UTM / `utm_tag`, Аналитика / `reach_analytics` |
| 10 | **Сообщество** / `community` | Relational | matching-engine (Random Coffee), mentorship-service, referral-tracker, амбассадорский учёт, event-organizer, initiative-coordinator | наставничество, пара Random Coffee, встреча 1:1, отзыв о встрече, реферальная связь, амбассадор, группа (сообщество, когорта), запрос помощи, отклик на помощь, событие сообщества (митап, воркшоп), инициатива развития, вклад члена | Наставничество / `mentorship`, Пара / `match`, Встреча / `meeting`, Отзыв / `meeting_feedback`, Реферал / `referral`, Амбассадор / `ambassador`, Группа / `group`, Вопрос-помощь / `help_request`, Отклик / `help_response`, Событие-сообщества / `community_event`, Инициатива / `initiative`, Вклад / `contribution` |
| 11 | **Лид** / `lead` | Proto-Persona | landing (форма регистрации), acquisition-funnel (UTM-трекер, веб-аналитика) | лид (Proto-Persona до Ory), UTM-заход, событие воронки (visit→signup→activation), claim (конверсия лида в Персону) | Лид / `lead`, UTM-заход / `utm_visit`, Событие-воронки / `funnel_event`, Конверсия / `claim` |
| 12 | **Награды** / `rewards` | Память.Observed + Derived | rewards-engine (начисление по правилам из Справочника), Методсовет (присвоение квалификации) | начисление баллов, ачивка, присвоение уровня квалификации (событие), текущий уровень квалификации (проекция), награда (приз), баланс | Начисление-баллов / `points_grant`, Ачивка / `achievement`, Переход-квалификации / `qualification_change`, Текущая-квалификация / `qualification_current`, Награда / `reward`, Баланс / `balance` |

</details>

### Категории WP-257 — покрытие

| Категория | Число БД | Какие |
|---|---|---|
| Персона | 1 | #1 |
| Память.Observed | 4 | #2, #3, #4, #9 |
| Память.Derived | 1 | #5 |
| Память.Observed+Derived (смешанные писатели) | 2 | #6, #12 |
| Platform-knowledge (проекция) | 1 | #7 |
| Catalog/Reference | 1 | #8 |
| Relational | 1 | #10 |
| Proto-Persona | 1 | #11 |
| Service/Ops | 0 | Вынесено на внешний SaaS (Д2) |

---

## 2. Карта 12 БД (обзор)

В каждом кластере — центральные физ.объекты БД по реестру §1. Межкластерные пунктирные стрелки с кратностями (1:1, 1:M, M:M) — связи между объектами разных БД по §4. Оранжевый кластер — внешняя LMS Aisystant (legacy, source-of-truth для исторических учебных данных, зеркалится в #6 и #12). Технические таблицы (`*_log`, `*_session`, `*_cache`, `*_state`, `*_snapshot`) не показаны на карте — они в §3 ERD по каждой БД.

```mermaid
flowchart LR
    classDef db_persona   fill:#e9d5ff,stroke:#7c3aed,color:#4c1d95
    classDef db_observed  fill:#dcfce7,stroke:#22c55e,color:#14532d
    classDef db_derived   fill:#fef9c3,stroke:#ca8a04,color:#713f12
    classDef db_relational fill:#fce7f3,stroke:#ec4899,color:#831843
    classDef db_reference fill:#dbeafe,stroke:#3b82f6,color:#1e3a5f
    classDef db_knowledge fill:#cffafe,stroke:#06b6d4,color:#164e63
    classDef db_proto     fill:#ffedd5,stroke:#f97316,color:#7c2d12
    classDef db_legacy    fill:#fef3c7,stroke:#d97706,color:#78350f

    subgraph DB1 ["#1 persona — Персона"]
      direction TB
      Acc1[Учётка]
      Decl1[Декларация о себе]
      Pref1[Предпочтения]
      Cap1[Захваты]
      Note1[Заметки]
      Col1[Коллекция-личная]
      Acc1 --- Decl1
      Acc1 --- Pref1
      Acc1 --- Cap1
      Acc1 --- Note1
      Acc1 --- Col1
    end
    class DB1 db_persona

    subgraph DB2 ["#2 journal — Журнал"]
      direction TB
      Ev2[Событие]
      Ses2[Сессия]
      Ctx2[Контекст-действия]
      Ses2 --- Ev2
      Ev2 --- Ctx2
    end
    class DB2 db_observed

    subgraph DB3 ["#3 payment — Платёж"]
      direction TB
      Payer3[Плательщик]
      Pay3[Платёж]
      Ref3[Возврат]
      Meth3[Метод-оплаты]
      Rec3[Чек]
      Payer3 --- Pay3
      Pay3 --- Ref3
      Pay3 --- Rec3
      Payer3 --- Meth3
    end
    class DB3 db_observed

    subgraph DB4 ["#4 subscription — Подписка"]
      direction TB
      Plan4[План]
      Grant4[Грант]
      Auto4[Автосписание]
      Hist4[История-грантов]
      Plan4 --- Grant4
      Grant4 --- Auto4
      Grant4 --- Hist4
    end
    class DB4 db_observed

    subgraph DB5 ["#5 indicators — Показатели"]
      direction TB
      Meas5[Замер]
      Base5[Baseline]
      Pot5[Потенциал]
      Ind5[Индикатор]
      Rcs5[RCS-ступень]
      Snap5[Снапшот]
      Meas5 --- Base5
      Base5 --- Ind5
      Pot5 --- Ind5
      Ind5 --- Rcs5
    end
    class DB5 db_derived

    subgraph DB6 ["#6 learning — Обучение"]
      direction TB
      Enr6[Зачисление]
      Prog6[Прогресс]
      Wb6[Рабочая-тетрадь]
      Asg6[ДЗ]
      Ans6[Ответ]
      Rev6[Проверка-наставника]
      Fd6[Лента-неделя]
      Enr6 --- Prog6
      Asg6 --- Wb6
      Asg6 --- Ans6
      Ans6 --- Rev6
      Enr6 --- Fd6
    end
    class DB6 db_observed

    subgraph DB7 ["#7 knowledge — Знание-платформы"]
      direction TB
      Col7[Коллекция-платформы]
      Doc7[Документ]
      Par7[Параграф]
      Emb7[Эмбеддинг]
      Conc7[Понятие]
      Rel7[Связь]
      Col7 --- Doc7
      Doc7 --- Par7
      Par7 --- Emb7
      Conc7 --- Rel7
      Par7 -. "о понятии" .- Conc7
    end
    class DB7 db_knowledge

    subgraph DB8 ["#8 reference — Справочник"]
      direction TB
      Tar8[Тариф]
      Stg8[Ступень-ученика]
      Ql8[Уровень-квалификации]
      Rule8[Правило-начисления]
      Prog8[Программа]
    end
    class DB8 db_reference

    subgraph DB9 ["#9 publication — Публикации"]
      direction TB
      Dr9[Черновик]
      Art9[Статья]
      Post9[Пост]
      Ch9[Канал]
      Ev9[Событие-публикации]
      Utm9[UTM]
      Dr9 --> Art9
      Art9 --> Post9
      Post9 --- Ch9
      Post9 --- Ev9
      Post9 --- Utm9
    end
    class DB9 db_observed

    subgraph DB10 ["#10 community — Сообщество"]
      direction TB
      Ment10[Наставничество]
      Match10[Пара]
      Meet10[Встреча]
      Help10[Вопрос-помощь]
      Init10[Инициатива]
      Contr10[Вклад]
      Amb10[Амбассадор]
      Grp10[Группа]
      Match10 --- Meet10
      Meet10 --- Help10
      Init10 --- Contr10
      Grp10 --- Amb10
    end
    class DB10 db_relational

    subgraph DB11 ["#11 lead — Лид"]
      direction TB
      Lead11[Лид]
      Utm11[UTM-заход]
      Fun11[Событие-воронки]
      Cl11[Конверсия]
      Lead11 --- Utm11
      Lead11 --- Fun11
      Fun11 --- Cl11
    end
    class DB11 db_proto

    subgraph DB12 ["#12 rewards — Награды"]
      direction TB
      Pts12[Начисление-баллов]
      Ach12[Ачивка]
      Qc12[Переход-квалификации]
      Qcur12[Текущая-квалификация]
      Rew12[Награда]
      Bal12[Баланс]
      Pts12 --- Bal12
      Qc12 --- Qcur12
      Ach12 --- Rew12
    end
    class DB12 db_observed

    subgraph DBLMS ["LMS Aisystant (legacy, внешний)"]
      direction TB
      UsrLMS[Пользователь-LMS]
      CrsLMS[Курс-LMS]
      PassLMS[Прохождение]
      QlLMS[Присвоение-квалификации]
      PayLMS[Платёж-LMS]
      UsrLMS --- PassLMS
      CrsLMS --- PassLMS
      UsrLMS --- QlLMS
      UsrLMS --- PayLMS
    end
    class DBLMS db_legacy

    %% ── Межкластерные связи (object → object, с кратностями) ──
    Acc1  -. "1:M совершает"            .-> Ev2
    Acc1  -. "1:1 плательщик-проекция"  .-> Payer3
    Acc1  -. "1:M владелец грантов"     .-> Grant4
    Acc1  -. "1:1 профиль Портного"     .-> Meas5
    Acc1  -. "1:M учащийся"             .-> Enr6
    Acc1  -. "1:M автор"                .-> Dr9
    Acc1  -. "1:M участник сообщества"  .-> Ment10
    Acc1  -. "1:M владелец баллов"      .-> Pts12
    Pay3  -. "1:1 активирует"           .-> Grant4
    Grant4 -. "1:M контекст замеров"    .-> Meas5
    Ev2   -. "M:1 сигнал для baseline"  .-> Base5
    Ev6Ref["события обучения"] -. "M:1" .-> Ev2
    Post9 -. "1:1 факт публикации"      .-> Ev2
    Cl11  -. "1:1 конверсия в Персону"  .-> Acc1
    Tar8  -. "1:M действует для"        .-> Plan4
    Rule8 -. "1:M применяется к"        .-> Pts12
    Ql8   -. "1:M присваивается"        .-> Qc12
    Stg8  -. "1:M проецируется"         .-> Rcs5
    Par7  -. "M:M освоение понятий"     .-> Meas5

    %% ── LMS (legacy) → наши БД ──
    UsrLMS  -. "1:1 ory_id зеркало"     .-> Acc1
    PassLMS -. "1:M коллектор событий"  .-> Ev2
    QlLMS   -. "1:1 source-of-truth"    .-> Qc12
    PayLMS  -. "1:1 webhook-зеркало"    .-> Pay3
    CrsLMS  -. "M:M курс ↔ зачисление"  .-> Enr6
```

**Легенда цветов кластеров:**
- **Фиолетовый** — Персона (writer = пользователь, owner = Git + Neon projection)
- **Зелёный** — Память.Observed (writer = платформа, событийная)
- **Жёлтый** — Память.Derived (writer = расчётный engine)
- **Розовый** — Relational (связи между персонами)
- **Голубой-светлый** — Catalog/Reference (writer = админ)
- **Голубой-тёмный** — Platform-knowledge (проекция общей онтологии)
- **Оранжевый-светлый** — Proto-Persona (до Ory)
- **Оранжевый-тёмный** — Legacy external (LMS Aisystant)

**Легенда кратностей** (ER-стиль): **1:1** (один-к-одному), **1:M** (один-ко-многим), **M:1** (много-к-одному), **M:M** (много-ко-многим). Внутри кластера кратности опущены для читаемости — показаны в §3 ERD по каждой БД через нотацию `||--o{`.

---

## 3. ERD по каждой БД

> **Методологическое основание:** `DP.METHOD.040` §1 — концептуальная ER только объекты физ.мира. HD «ER ≠ Физ.схема», «Объект ≠ Отношение» (distinctions.md). Детальная физ.схема с колонками, типами и FK **[документ не создан, планируется в WP-253 Ф2 как `DP.ARCH.004-physical-schema.md`]**. Не показаны: `*_log`, `*_cache`, `*_state`, `*_snapshot`, `*_staging`, промежуточные M:N без атрибутов.

### 3.1 #1 persona — Персона

**Категория WP-257:** Персона. **Writer:** пользователь через любой интерфейс (VS Code, бот, веб, CLI) + personal-indexer (эмбеддинги PACK-personal и DS-my-strategy Git-репо). **Owner:** Git пользователя, Neon хранит проекцию.

**BC:** Personal Declaration & Personal Knowledge Projection.

```mermaid
erDiagram
    ACCOUNT ||--|| DECLARATION : "имеет"
    ACCOUNT ||--|| PREFERENCES : "имеет"
    ACCOUNT ||--o{ CAPTURE : "фиксирует"
    ACCOUNT ||--o{ NOTE : "ведёт"
    ACCOUNT ||--o{ COLLECTION : "владеет"
    COLLECTION ||--o{ DOCUMENT : "содержит"
    DOCUMENT ||--o{ PARAGRAPH : "разбит"
    PARAGRAPH ||--|| EMBEDDING : "индексирован"

    ACCOUNT {
        uuid account_id PK
        uuid ory_id FK "ссылка на Ory Kratos"
        timestamp created_at
        timestamp last_sync
    }
    DECLARATION {
        uuid account_id FK
        jsonb roles "роли созидателя"
        jsonb values "ценности"
        jsonb goals "цели"
        text mission "миссия"
    }
    PREFERENCES {
        uuid account_id FK
        jsonb claude_md "CLAUDE.md preferences"
        jsonb extensions "VS Code extensions"
        jsonb channels "предпочтения каналов"
    }
    CAPTURE {
        uuid capture_id PK
        uuid account_id FK
        text content
        text source "agent/manual"
        timestamp captured_at
        boolean accepted "acceptance by user"
    }
    NOTE {
        uuid note_id PK
        uuid account_id FK
        text content
        text kind "fleeting/permanent"
        timestamp updated_at
    }
    COLLECTION {
        uuid collection_id PK
        uuid account_id FK
        text namespace "personal.pack | personal.strategy"
        text version
    }
    DOCUMENT {
        uuid document_id PK
        uuid collection_id FK
        text path "git path"
        text commit_sha
    }
    PARAGRAPH {
        uuid paragraph_id PK
        uuid document_id FK
        int position
        text content
    }
    EMBEDDING {
        uuid paragraph_id FK
        vector embedding "pgvector"
        text model
    }
```

**Инвариант:** каждая запись в `account` соответствует одной записи в Ory Kratos (1:1 через `ory_id`). Удаление учётки в Neon ≠ удаление в Ory (Ory — внешняя система, см. Д1).

**Проекции:** таблицы `document`/`paragraph`/`embedding` — проекции Git-репо пользователя, rebuildable при `git pull` + reindex.

### 3.2 #2 journal — Журнал

**Категория WP-257:** Память.Observed. **Writer:** единый event-коллектор (бот, веб, VS Code, CLI шлют события через gateway). **Owner:** Neon.

**BC:** User Actions Event Stream.

```mermaid
erDiagram
    SESSION ||--o{ EVENT : "содержит"
    EVENT ||--|| ACTION_CONTEXT : "описан"
    ACCOUNT }o--|| SESSION : "инициирует"

    SESSION {
        uuid session_id PK
        uuid account_id FK
        text channel "bot/web/vscode/cli"
        timestamp started_at
        timestamp ended_at
    }
    EVENT {
        uuid event_id PK
        uuid session_id FK
        text kind "open/read/answer/click/…"
        text actor "user/agent"
        timestamp occurred_at
    }
    ACTION_CONTEXT {
        uuid event_id FK
        text agent "which agent"
        text artifact "what artifact"
        jsonb payload "event-specific data"
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** append-only (события не редактируются после записи). Retention: 90 дней в hot-path, архив в cold-storage по политике §7.

**Связи кросс-БД:** события из `#6 learning`, `#9 publication`, `#11 lead` могут проецироваться как `EVENT` в `#2 journal` через outbox-pattern (для единой ленты действий пользователя).

### 3.3 #3 payment — Платёж

**Категория WP-257:** Память.Observed. **Writer:** billing-webhook (ЮKassa, Telegram Stars), админ через Directus. **Owner:** Neon.

**BC:** Billing & Payment Reception.

```mermaid
erDiagram
    PAYER ||--o{ PAYMENT : "совершает"
    PAYER ||--o{ PAYMENT_METHOD : "владеет"
    PAYMENT ||--o| REFUND : "может иметь"
    PAYMENT ||--|| RECEIPT : "выдан чек"
    PAYMENT_METHOD ||--o{ PAYMENT : "использован в"

    PAYER {
        uuid payer_id PK
        uuid account_id FK "nullable - может быть юрлицом"
        text kind "individual/legal_entity"
        text inn "для юрлиц"
        text name
    }
    PAYMENT {
        uuid payment_id PK
        uuid payer_id FK
        uuid payment_method_id FK
        text provider "yookassa/tg_stars"
        numeric amount
        text currency
        text status "succeeded/pending/failed"
        timestamp paid_at
    }
    REFUND {
        uuid refund_id PK
        uuid payment_id FK
        numeric amount
        text reason
        timestamp refunded_at
    }
    PAYMENT_METHOD {
        uuid payment_method_id PK
        uuid payer_id FK
        text kind "card/stars/bank"
        text masked_id "payment_credentials class"
    }
    RECEIPT {
        uuid receipt_id PK
        uuid payment_id FK
        text fiscal_number
        jsonb items
    }
```

**Инвариант:** `PAYMENT.payer_id` — не `account_id` напрямую (плательщик ≠ персона 1:1; юрлицо может оплачивать за сотрудника-ученика). Связь с Персоной — через `PAYER.account_id` (nullable).

**Класс чувствительности:** `PAYMENT_METHOD.masked_id` = `payment_credentials` (строже PII — см. HD «PII ≠ payment_credentials»). Логирование строго запрещено, хранение только зашифрованным.

### 3.4 #4 subscription — Подписка

**Категория WP-257:** Память.Observed. **Writer:** subscription-service (создание грантов, продление, отмена, пауза). **Owner:** Neon.

**BC:** Subscription Rights & Lifecycle.

```mermaid
erDiagram
    PLAN ||--o{ GRANT : "выдаётся по"
    ACCOUNT ||--o{ GRANT : "получает"
    GRANT ||--o| AUTOPAY : "может иметь"
    GRANT ||--o{ GRANT_HISTORY : "эволюционирует"

    PLAN {
        uuid plan_id PK
        uuid tariff_id FK "ссылка на #8 reference"
        text name
        interval duration
        numeric price
    }
    GRANT {
        uuid grant_id PK
        uuid account_id FK
        uuid plan_id FK
        uuid activating_payment_id FK "ссылка на #3 payment"
        timestamp valid_from
        timestamp valid_to
        text status "active/paused/expired/cancelled"
    }
    AUTOPAY {
        uuid autopay_id PK
        uuid grant_id FK
        uuid payment_method_id FK "ссылка на #3"
        date next_charge
        text schedule
    }
    GRANT_HISTORY {
        uuid history_id PK
        uuid grant_id FK
        text event "created/renewed/paused/resumed/cancelled"
        timestamp occurred_at
        jsonb snapshot
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** у одного `account_id` может быть несколько активных грантов (семейный тариф, gift-подписки), но только один `primary` на каждый тип программы (ЛР/РР). Период `[valid_from, valid_to)` — полуоткрытый.

### 3.5 #5 indicators — Показатели

**Категория WP-257:** Память.Derived. **Writer:** Портной-engine (расчёт baseline, potential, индикаторов, RCS-ступени, проекций). **Owner:** Neon.

**BC:** Derived User Profile (было «ЦД» в v1).

```mermaid
erDiagram
    ACCOUNT ||--o{ MEASUREMENT : "замеряется"
    MEASUREMENT }o--|| BASELINE : "сглаживается в"
    BASELINE ||--|| POTENTIAL : "имеет потолок"
    BASELINE }o--|| INDICATOR : "входит в композит"
    POTENTIAL }o--|| INDICATOR : "ограничивает"
    INDICATOR }o--|| RCS_CURRENT : "определяет"
    ACCOUNT ||--o| RCS_CURRENT : "на ступени"
    ACCOUNT ||--o{ SNAPSHOT : "кэш on-demand"

    MEASUREMENT {
        uuid measurement_id PK
        uuid account_id FK
        text characteristic "собранность/скорость/…"
        text indicator_code "slots_per_week/…"
        numeric value "now - зашумлён"
        timestamp measured_at
    }
    BASELINE {
        uuid account_id FK
        text indicator_code
        numeric value "устойчивое"
        text window "4w/8w/…"
        timestamp calculated_at
    }
    POTENTIAL {
        uuid account_id FK
        text indicator_code
        numeric ceiling "потолок на момент t"
        timestamp calculated_at
    }
    INDICATOR {
        uuid indicator_id PK
        uuid account_id FK
        text name "derived-композит"
        jsonb formula "какие baseline/potential входят"
        numeric value
        timestamp calculated_at
    }
    RCS_CURRENT {
        uuid account_id FK
        int stage "1-5"
        text stage_name "Случайный/Практикующий/…"
        jsonb rationale "какие индикаторы дали эту ступень"
        timestamp updated_at
    }
    SNAPSHOT {
        uuid snapshot_id PK
        uuid account_id FK
        text source "git.pack-personal/git.ds-strategy/…"
        jsonb content
        timestamp fetched_at
        timestamp expires_at "TTL"
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** `BASELINE.value` — результат расчёта по окну `MEASUREMENT.value` (скользящее среднее/медиана); `POTENTIAL.ceiling` — не мгновенное, а целевой уровень с учётом фундаментального развития (годы). `RCS_CURRENT.stage` — derived-проекция из `INDICATOR` (не writable снаружи Портного).

**Решение WP-257 Ф2:** таблица называется `rcs_current` (не `stage`), чтобы не путалась с уровнем квалификации в `#12 rewards.qualification_current` (разные шкалы, разные писатели). См. HD «Характеристика ≠ Показатель ≠ Значение ≠ Потенциал».

**SNAPSHOT — кэш on-demand:** Портной не должен лазить в Git пользователя каждый расчёт. `SNAPSHOT` = cache pull-данных из Git с TTL. При просрочке → invalidate → re-fetch.

### 3.6 #6 learning — Обучение

**Категория WP-257:** Память.Observed + Derived. **Writer:** learning-service, наставники через Directus, scheduler лент/дайджестов. **Owner:** Neon.

**BC:** Learning Progress & Mentorship Workflow.

```mermaid
erDiagram
    ACCOUNT ||--o{ ENROLLMENT : "зачислен"
    ENROLLMENT ||--o{ COURSE_PROGRESS : "прогрессирует"
    ENROLLMENT ||--o{ FEED_WEEK : "получает ленту"
    FEED_WEEK ||--o{ FEED_SESSION : "содержит"
    ENROLLMENT ||--o{ WORKBOOK : "ведёт"
    ASSIGNMENT ||--o{ ANSWER : "получает ответы"
    WORKBOOK ||--o{ ANSWER : "содержит"
    ANSWER ||--o| ASSESSMENT : "оценён"
    ANSWER ||--o| MENTOR_REVIEW : "проверен"
    ENROLLMENT ||--o{ PRACTICE_ATTEMPT : "тренируется"
    ENROLLMENT ||--o{ QA_PAIR : "задаёт вопросы"
    ENROLLMENT ||--o{ MARATHON : "участвует"
    ENROLLMENT ||--o{ FEEDBACK : "даёт отзывы"

    ENROLLMENT {
        uuid enrollment_id PK
        uuid account_id FK
        uuid program_id FK "ссылка на #8 reference"
        text status "active/paused/completed"
        timestamp enrolled_at
    }
    COURSE_PROGRESS {
        uuid progress_id PK
        uuid enrollment_id FK
        text section
        numeric completion
        timestamp updated_at
    }
    WORKBOOK {
        uuid workbook_id PK
        uuid enrollment_id FK
        text name
    }
    ASSIGNMENT {
        uuid assignment_id PK
        text code
        text title
    }
    ANSWER {
        uuid answer_id PK
        uuid workbook_id FK
        uuid assignment_id FK
        text content
        timestamp submitted_at
    }
    ASSESSMENT {
        uuid assessment_id PK
        uuid answer_id FK
        text grade
        jsonb rubric
    }
    MENTOR_REVIEW {
        uuid review_id PK
        uuid answer_id FK
        uuid mentor_account_id FK
        text comment
        timestamp reviewed_at
    }
    PRACTICE_ATTEMPT {
        uuid attempt_id PK
        uuid enrollment_id FK
        text exercise_code
        jsonb result
        timestamp attempted_at
    }
    QA_PAIR {
        uuid qa_id PK
        uuid enrollment_id FK
        text question
        text answer
        timestamp asked_at
    }
    MARATHON {
        uuid marathon_id PK
        uuid enrollment_id FK
        text name
        date start_date
        date end_date
    }
    FEEDBACK {
        uuid feedback_id PK
        uuid enrollment_id FK
        text topic
        text content
        timestamp given_at
    }
    FEED_WEEK {
        uuid feed_week_id PK
        uuid enrollment_id FK
        int week_number
        date week_start
    }
    FEED_SESSION {
        uuid feed_session_id PK
        uuid feed_week_id FK
        timestamp scheduled_for
        text content_ref
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** `MENTOR_REVIEW.mentor_account_id` ссылается на `ACCOUNT` с активным `MENTORSHIP` в `#10 community` (наставник — это роль, не атрибут учётки).

**Legacy source:** таблицы `COURSE_PROGRESS`, `WORKBOOK`, `ANSWER` во время миграции читают из LMS Aisystant через bridge (`coursepassing`/`taskanswer`), до WP-254 Ф5.

### 3.7 #7 knowledge — Знание-платформы

**Категория WP-257:** Platform-knowledge (projection). **Writer:** knowledge-mcp индексатор (читает платформенные Pack Git-репо и эмбеддит). **Owner:** Neon (как проекция Git).

**BC:** Platform Ontology Projection.

```mermaid
erDiagram
    INDEX_VERSION ||--o{ COLLECTION : "содержит"
    COLLECTION ||--o{ DOCUMENT : "группирует"
    DOCUMENT ||--o{ PARAGRAPH : "разбит"
    PARAGRAPH ||--|| EMBEDDING : "индексирован"
    CONCEPT ||--o{ RELATION : "связан"
    RELATION }o--|| CONCEPT : "направлен к"
    PARAGRAPH }o--o{ CONCEPT : "о понятии"

    INDEX_VERSION {
        uuid version_id PK
        text label "v1.2.3"
        timestamp built_at
        text builder "knowledge-mcp@commit_sha"
    }
    COLLECTION {
        uuid collection_id PK
        uuid version_id FK
        text namespace "platform.digital-platform | platform.MIM | …"
        text source_repo
        text commit_sha
    }
    DOCUMENT {
        uuid document_id PK
        uuid collection_id FK
        text path
        text title
        timestamp last_modified
    }
    PARAGRAPH {
        uuid paragraph_id PK
        uuid document_id FK
        int position
        text content
    }
    EMBEDDING {
        uuid paragraph_id FK
        vector embedding "pgvector"
        text model
    }
    CONCEPT {
        uuid concept_id PK
        text label
        text domain "platform/MIM/ecosystem"
        text definition
    }
    RELATION {
        uuid relation_id PK
        uuid from_concept FK
        uuid to_concept FK
        text kind "subclass/part-of/depends-on/…"
    }
```

**Инвариант:** `COLLECTION.namespace` префиксом отделяет платформенные коллекции (`platform.*`) от личных (`personal.*` — живут в `#1 persona`). Переиндексация новой `INDEX_VERSION` не ломает активные запросы (старая версия доступна до переключения).

**Concept graph:** 3503 рёбер / 1180 понятий / 344 переведено — актуальная статистика на 22 апр 2026 (WP-242).

### 3.8 #8 reference — Справочник

**Категория WP-257:** Catalog/Reference. **Writer:** админ через Directus (редкие правки, source-of-truth = реестры + решения Методсовета). **Owner:** Neon.

**BC:** Platform Reference Data.

```mermaid
erDiagram
    PROGRAM ||--o{ TARIFF : "имеет тарифы"
    PROGRAM ||--o{ RCS_STAGE : "имеет ступени"
    QUALIFICATION_LEVEL ||--o{ REWARD_RULE : "связан с правилами"
    REWARD_RULE ||--o{ MULTIPLIER : "применяет"

    PROGRAM {
        uuid program_id PK
        text code "personal_development/work_development"
        text name
        text description
    }
    TARIFF {
        uuid tariff_id PK
        uuid program_id FK
        text name
        numeric price
        interval duration
        boolean active
    }
    RCS_STAGE {
        int stage_number PK "1-5"
        uuid program_id FK
        text name "Случайный/Практикующий/…"
        text description
        jsonb rubric "baseline-требования"
    }
    QUALIFICATION_LEVEL {
        int level_number PK "1-11"
        text name "Интересант/…/Общественный деятель"
        text description
        text assessment_method
    }
    REWARD_RULE {
        uuid rule_id PK
        text trigger "действие/ступень/квалификация"
        text reward_kind "points/achievement"
        numeric amount
        uuid qualification_level_id FK "nullable"
    }
    MULTIPLIER {
        uuid multiplier_id PK
        uuid rule_id FK
        text condition
        numeric factor
    }
    CONSTANT {
        text key PK
        jsonb value
        text description
    }
```

**Инвариант:** справочник обновляется редко и только админом. Все таблицы имеют `valid_from` / `valid_to` для историзации (temporal validity как перпендикулярный атрибут, WP-257).

### 3.9 #9 publication — Публикации

**Категория WP-257:** Память.Observed. **Writer:** content-pipeline (создание → публикация → каналы). **Owner:** Neon.

**BC:** Content Publishing Pipeline.

```mermaid
erDiagram
    ACCOUNT ||--o{ DRAFT : "автор"
    DRAFT ||--o| ARTICLE : "становится"
    ARTICLE ||--o{ POST : "публикуется как"
    POST }o--|| CHANNEL : "в канал"
    POST ||--|| PUBLICATION_EVENT : "факт публикации"
    POST ||--o{ UTM_TAG : "метит"
    POST ||--o| REACH_ANALYTICS : "аналитика охвата"

    DRAFT {
        uuid draft_id PK
        uuid account_id FK
        text title
        text content
        timestamp created_at
    }
    ARTICLE {
        uuid article_id PK
        uuid draft_id FK
        text title
        text content_md
        timestamp finalized_at
    }
    POST {
        uuid post_id PK
        uuid article_id FK
        uuid channel_id FK
        text format "text/image/video/thread"
        timestamp scheduled_for
        timestamp published_at
    }
    CHANNEL {
        uuid channel_id PK
        text platform "telegram/vk/linkedin/…"
        text handle
        text owner_account_id
    }
    PUBLICATION_EVENT {
        uuid event_id PK
        uuid post_id FK
        text status "scheduled/published/failed"
        timestamp occurred_at
    }
    UTM_TAG {
        uuid tag_id PK
        uuid post_id FK
        text utm_source
        text utm_medium
        text utm_campaign
    }
    REACH_ANALYTICS {
        uuid post_id FK
        int impressions
        int clicks
        int reactions
        timestamp last_updated
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** `ARTICLE.draft_id` — 1:1 с черновиком после финализации. `POST` может иметь несколько публикаций одного `ARTICLE` в разные каналы (мультиканальный publisher, WP-129).

### 3.10 #10 community — Сообщество

**Категория WP-257:** Relational (связи Persona↔Persona). **Writer:** matching-engine (Random Coffee), mentorship-service, referral-tracker, амбассадорский учёт, event-organizer, initiative-coordinator. **Owner:** Neon.

**BC:** Community Relations & Mutual Support.

```mermaid
erDiagram
    MENTORSHIP }o--|| ACCOUNT : "наставник"
    MENTORSHIP }o--|| ACCOUNT : "ученик"
    MATCH }o--|| ACCOUNT : "персона-A"
    MATCH }o--|| ACCOUNT : "персона-B"
    MATCH ||--o{ MEETING : "приводит к"
    MEETING ||--o{ MEETING_FEEDBACK : "получает"
    REFERRAL }o--|| ACCOUNT : "реферер"
    REFERRAL }o--|| ACCOUNT : "реферал"
    AMBASSADOR }o--|| ACCOUNT : "роль"
    GROUP ||--o{ AMBASSADOR : "имеет"
    HELP_REQUEST }o--|| ACCOUNT : "спрашивает"
    HELP_REQUEST ||--o{ HELP_RESPONSE : "получает"
    HELP_RESPONSE }o--|| ACCOUNT : "отвечает"
    COMMUNITY_EVENT }o--|| GROUP : "проводится в"
    COMMUNITY_EVENT ||--o{ CONTRIBUTION : "получает"
    INITIATIVE }o--|| ACCOUNT : "автор"
    INITIATIVE ||--o{ CONTRIBUTION : "получает"

    MENTORSHIP {
        uuid mentorship_id PK
        uuid mentor_account_id FK
        uuid learner_account_id FK
        text role "TM1/TM2/TM3"
        timestamp started_at
        timestamp ended_at
    }
    MATCH {
        uuid match_id PK
        uuid account_a FK
        uuid account_b FK
        text kind "random_coffee/topic/…"
        timestamp matched_at
    }
    MEETING {
        uuid meeting_id PK
        uuid match_id FK
        timestamp scheduled_for
        text outcome
    }
    MEETING_FEEDBACK {
        uuid feedback_id PK
        uuid meeting_id FK
        uuid from_account FK
        int rating
        text comment
    }
    REFERRAL {
        uuid referral_id PK
        uuid referrer_id FK
        uuid referred_id FK
        timestamp referred_at
    }
    AMBASSADOR {
        uuid ambassador_id PK
        uuid account_id FK
        uuid group_id FK
        text status "active/on-leave"
    }
    GROUP {
        uuid group_id PK
        text name
        text kind "community/cohort/club"
    }
    HELP_REQUEST {
        uuid request_id PK
        uuid account_id FK
        text topic
        text description
        timestamp asked_at
        text status "open/resolved/closed"
    }
    HELP_RESPONSE {
        uuid response_id PK
        uuid request_id FK
        uuid account_id FK
        text content
        timestamp responded_at
    }
    COMMUNITY_EVENT {
        uuid event_id PK
        uuid group_id FK
        text kind "meetup/workshop/review"
        timestamp held_at
        text agenda
    }
    INITIATIVE {
        uuid initiative_id PK
        uuid account_id FK
        text title
        text description
        text status "proposed/active/completed"
    }
    CONTRIBUTION {
        uuid contribution_id PK
        uuid account_id FK
        uuid initiative_id FK "nullable"
        uuid event_id FK "nullable"
        text kind "organized/led/summarized/…"
        timestamp contributed_at
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** `MENTORSHIP.mentor_account_id` — это `ACCOUNT`, у которого есть активный `GRANT` на роль наставника (право проверять ДЗ), подтверждаемое через Keto policy. `MATCH` не превращается в `MEETING` автоматически — нужно действие пользователя (accept).

### 3.11 #11 lead — Лид

**Категория WP-257:** Proto-Persona. **Writer:** landing (форма регистрации), acquisition-funnel (UTM-трекер, веб-аналитика). **Owner:** Neon. **После claim** — учётка переезжает в `#1 persona`.

**BC:** Pre-Ory Acquisition.

```mermaid
erDiagram
    LEAD ||--o{ UTM_VISIT : "пришёл с"
    LEAD ||--o{ FUNNEL_EVENT : "движется по воронке"
    LEAD ||--o| CLAIM : "конвертирован"

    LEAD {
        uuid lead_id PK
        text email
        text tg_username
        timestamp first_seen_at
        jsonb self_declaration "что указал на лендинге"
    }
    UTM_VISIT {
        uuid visit_id PK
        uuid lead_id FK
        text utm_source
        text utm_medium
        text utm_campaign
        timestamp visited_at
    }
    FUNNEL_EVENT {
        uuid event_id PK
        uuid lead_id FK
        text stage "visit/signup/activation"
        timestamp occurred_at
        jsonb payload
    }
    CLAIM {
        uuid claim_id PK
        uuid lead_id FK
        uuid account_id "FK to #1 persona after claim"
        timestamp claimed_at
    }
```

**Инвариант:** `LEAD` существует без `account_id` (до регистрации в Ory). `CLAIM` — одноразовое событие конверсии; после него PII-данные лида мигрируют в `#1 persona.account`, остальное остаётся в `#11` для аналитики воронки.

### 3.12 #12 rewards — Награды

**Категория WP-257:** Память.Observed + Derived. **Writer:** rewards-engine (начисление по правилам из `#8 reference`), Методсовет (присвоение квалификации через Directus). **Owner:** Neon.

**BC:** Points, Achievements, Qualifications.

```mermaid
erDiagram
    ACCOUNT ||--o{ POINTS_GRANT : "получает"
    ACCOUNT ||--o{ ACHIEVEMENT : "заслуживает"
    ACCOUNT ||--o{ QUALIFICATION_CHANGE : "переходит"
    ACCOUNT ||--|| QUALIFICATION_CURRENT : "имеет текущий"
    ACCOUNT ||--o| BALANCE : "имеет баланс"
    POINTS_GRANT }o--|| REWARD_RULE : "по правилу"
    QUALIFICATION_CHANGE }o--|| QUALIFICATION_LEVEL : "на уровень"
    ACHIEVEMENT }o--|| REWARD : "приз"

    POINTS_GRANT {
        uuid grant_id PK
        uuid account_id FK
        uuid rule_id FK "ссылка на #8 reference"
        numeric amount
        text reason
        timestamp granted_at
    }
    ACHIEVEMENT {
        uuid achievement_id PK
        uuid account_id FK
        uuid reward_id FK
        text code "first_week/mentor_rank/…"
        timestamp earned_at
    }
    QUALIFICATION_CHANGE {
        uuid change_id PK
        uuid account_id FK
        int level_number FK "ссылка на #8 qualification_level"
        text method "methodsovet/automatic/exam"
        text rationale
        timestamp changed_at
    }
    QUALIFICATION_CURRENT {
        uuid account_id FK
        int level_number FK
        timestamp updated_at
    }
    REWARD {
        uuid reward_id PK
        text name
        text kind "badge/certificate/physical"
        text description
    }
    BALANCE {
        uuid account_id FK
        numeric points
        timestamp last_updated
    }
    REWARD_RULE {
        uuid rule_id PK
    }
    QUALIFICATION_LEVEL {
        int level_number PK
    }
    ACCOUNT {
        uuid account_id PK
    }
```

**Инвариант:** `QUALIFICATION_CURRENT` — проекция (derived) последнего `QUALIFICATION_CHANGE`. Перестраивается при каждом change. `BALANCE` — агрегат `POINTS_GRANT` минус списания. Отличие от RCS-ступени в `#5 indicators.rcs_current`: RCS двигает Портной (расчёт), квалификацию двигает Методсовет (решение).

### 3.13 External: LMS Aisystant (legacy)

**Категория WP-257:** вне пользовательской модели (внешняя система). **Writer:** LMS-команда (Дима). **Owner:** отдельная БД LMS (не Neon платформы). **Наше отношение:** read-only через bridges для миграции исторических данных.

**BC:** Legacy Learning Management (источник истории, зеркалится в #6, #12, #3).

```mermaid
erDiagram
    SUSER ||--o{ COURSEPASSING : "проходит"
    COURSE ||--o{ COURSEPASSING : "проходится"
    COURSE ||--o{ SECTION : "разбит"
    SECTION ||--o{ SECTIONPASSING : "проходится"
    SUSER ||--o{ TASKANSWER : "отвечает"
    TASK ||--o{ TASKANSWER : "получает"
    TASKANSWER }o--|| SUSER : "проверяет наставник"
    SUSER ||--o{ QUALIFICATION_LEVEL_EVENT : "получает уровень"
    SUSER ||--o{ PAYMENT_LMS : "платит"
    SUSER ||--o{ USER_EVENT : "участвует"
    EVENT ||--o{ USER_EVENT : "набирает участников"
    SUSER ||--o{ KONTRAGENT : "контрагент"

    SUSER {
        int id PK
        text ory_id "nullable до миграции"
        text tg_id
        text email
        text role "student/mentor/admin"
    }
    COURSE {
        int id PK
        text name
    }
    SECTION {
        int id PK
        int course_id FK
        text name
    }
    COURSEPASSING {
        int id PK
        int suser_id FK
        int course_id FK
        timestamp started_at
    }
    SECTIONPASSING {
        int id PK
        int coursepassing_id FK
        int section_id FK
        timestamp passed_at
    }
    TASK {
        int id PK
        int section_id FK
        text content
    }
    TASKANSWER {
        int id PK
        int suser_id FK
        int task_id FK
        text answer
        int reviewer_suser_id FK
    }
    QUALIFICATION_LEVEL_EVENT {
        int id PK
        int suser_id FK
        int level_number
        timestamp event_at
    }
    PAYMENT_LMS {
        int id PK
        int suser_id FK
        numeric amount
        timestamp paid_at
    }
    EVENT {
        int id PK
        text name
    }
    USER_EVENT {
        int id PK
        int suser_id FK
        int event_id FK
        timestamp registered_at
    }
    KONTRAGENT {
        int id PK
        int suser_id FK "nullable"
        text kind "individual/legal"
    }
```

**Роль в миграции:** LMS остаётся source-of-truth до WP-254 (миграция 9 учебных объектов). `QUALIFICATION_LEVEL_EVENT` — единственный source-of-truth квалификаций до эстафеты в `#12 rewards.qualification_change`. Bridge — read-only, writes блокированы.

---

## 4. Связи между БД (межкластерные)

| Связь | От (БД.Объект) | К (БД.Объект) | Кратность | Тип | Комментарий |
|---|---|---|---|---|---|
| Пользовательская идентичность | #1.ACCOUNT.ory_id | Ory Kratos (внешняя) | 1:1 | FK | Persona layer, Д1 |
| Плательщик-Персона | #3.PAYER.account_id | #1.ACCOUNT | M:1 (nullable) | FK | Юрлицо может платить за другого |
| Активация гранта | #3.PAYMENT | #4.GRANT | 1:1 | FK | `activating_payment_id` |
| Грант-Персона | #4.GRANT.account_id | #1.ACCOUNT | M:1 | FK | |
| Тариф плана | #4.PLAN.tariff_id | #8.TARIFF | M:1 | FK | Справочник |
| Замер-Персона | #5.MEASUREMENT.account_id | #1.ACCOUNT | M:1 | FK | |
| Grant → контекст замеров | #4.GRANT | #5.MEASUREMENT | 1:M | логическая | Замер имеет смысл в контексте активного гранта |
| Baseline ← события | #2.EVENT | #5.BASELINE | M:1 | агрегация | Портной читает события, пишет baseline |
| RCS-ступень ← справочник | #5.RCS_CURRENT.stage | #8.RCS_STAGE | M:1 | FK | |
| Зачисление-Персона | #6.ENROLLMENT.account_id | #1.ACCOUNT | M:1 | FK | |
| Программа зачисления | #6.ENROLLMENT.program_id | #8.PROGRAM | M:1 | FK | |
| Наставник в проверке | #6.MENTOR_REVIEW.mentor_account_id | #1.ACCOUNT (+ #10.MENTORSHIP) | M:1 | FK + Keto | |
| События обучения → Журнал | #6.* (events) | #2.EVENT | 1:1 | outbox projection | |
| Коллекция платформы | #7.COLLECTION.namespace | PACK-* (Git) | M:1 | проекция | |
| Параграф → Понятие | #7.PARAGRAPH | #7.CONCEPT | M:M | |  |
| Авторство Публикации | #9.DRAFT.account_id | #1.ACCOUNT | M:1 | FK | |
| Событие Публикации → Журнал | #9.PUBLICATION_EVENT | #2.EVENT | 1:1 | outbox | |
| Сообщество-Персона | #10.* | #1.ACCOUNT | M:M | через роли | Mentorship, Match, Ambassador — все через FK |
| Конверсия Лида | #11.CLAIM.account_id | #1.ACCOUNT | 1:1 | FK | После claim |
| Правило начисления | #12.POINTS_GRANT.rule_id | #8.REWARD_RULE | M:1 | FK | |
| Уровень квалификации | #12.QUALIFICATION_CHANGE.level_number | #8.QUALIFICATION_LEVEL | M:1 | FK | |
| Баланс-Персона | #12.BALANCE.account_id | #1.ACCOUNT | 1:1 | FK | |

### Цветовая схема типов связей

- **FK** — прямая внешнеключевая связь в БД
- **Проекция** — rebuildable данные из другого источника
- **Outbox** — асинхронное копирование события в стрим
- **Агрегация** — writer читает источник, вычисляет, пишет результат
- **Логическая** — смысловая связь, не обязательно enforced на уровне БД (cross-DB)
- **Keto** — авторизация через политики Keto (Ory)

---

## 5. Потоки

### 5.1 Поток идентичности и доступа

От первого касания на лендинге до активной подписки.

```mermaid
sequenceDiagram
    actor User as Посетитель
    participant Landing as Лендинг
    participant Lead as #11 lead
    participant Ory as Ory Kratos
    participant Persona as #1 persona
    participant Pay as #3 payment
    participant Sub as #4 subscription
    participant Keto as Ory Keto

    User->>Landing: visit (UTM)
    Landing->>Lead: UTM_VISIT + LEAD
    User->>Landing: signup form
    Landing->>Lead: FUNNEL_EVENT (signup)
    Landing->>Ory: register identity
    Ory-->>Landing: ory_id
    Landing->>Lead: CLAIM (lead_id → account_id)
    Landing->>Persona: ACCOUNT (с ory_id)
    User->>Pay: оплачивает
    Pay->>Pay: PAYMENT + RECEIPT
    Pay->>Sub: активация GRANT (activating_payment_id)
    Sub->>Keto: policy: user has access
    User->>Keto: request resource
    Keto-->>User: allow/deny
```

### 5.2 Поток событий → Показатели (Память.Observed → Память.Derived)

Как первичные действия превращаются в baseline и RCS-ступень.

```mermaid
sequenceDiagram
    actor User as Пользователь
    participant Client as Клиент (бот/web/vscode)
    participant Gw as Event Gateway
    participant Journal as #2 journal
    participant Learn as #6 learning
    participant Pub as #9 publication
    participant Tailor as Портной (engine)
    participant Ind as #5 indicators

    User->>Client: действие (чтение/ответ/клик)
    Client->>Gw: event + context
    Gw->>Journal: EVENT + SESSION
    alt действие обучающее
      Client->>Learn: ANSWER/ASSESSMENT
      Learn->>Journal: outbox projection
    end
    alt действие публикации
      Client->>Pub: POST/PUBLICATION_EVENT
      Pub->>Journal: outbox projection
    end
    Tailor->>Journal: читает EVENT за окно
    Tailor->>Learn: читает прогресс (опц.)
    Tailor->>Ind: MEASUREMENT (замеры сейчас)
    Tailor->>Ind: BASELINE (сглаживание)
    Tailor->>Ind: INDICATOR (композиты)
    Tailor->>Ind: RCS_CURRENT (derived ступень)
    Note over Tailor,Ind: on-demand pull Pack из Git → SNAPSHOT (кэш TTL)
```

### 5.3 Поток содержания → Публикации

От черновика до многоканальной публикации.

```mermaid
sequenceDiagram
    actor Author as Автор
    participant Persona as #1 persona
    participant Pipe as content-pipeline
    participant Pub as #9 publication
    participant TG as Telegram
    participant VK as VK
    participant Journal as #2 journal
    participant Ind as #5 indicators

    Author->>Persona: создаёт DRAFT (личный)
    Author->>Pipe: финализирует → ARTICLE
    Pipe->>Pub: ARTICLE
    Pipe->>Pub: POST (несколько каналов)
    Pub->>TG: публикация
    Pub->>VK: публикация
    TG-->>Pub: PUBLICATION_EVENT (published)
    VK-->>Pub: PUBLICATION_EVENT (published)
    Pub->>Journal: outbox (факт публикации = событие автора)
    Journal->>Ind: сигнал для Портного (через Event Gateway)
```

---

## 6. Писатели и читатели по БД

| БД | Пишут | Читают |
|---|---|---|
| #1 persona | пользователь (через VS Code/бот/веб/CLI), personal-indexer | Портной, Навигатор, Оркестратор, все клиент-интерфейсы, knowledge-search |
| #2 journal | event-gateway (от всех клиентов), outbox из #6/#9/#11 | Портной, Аналитика (Metabase view), Navigator |
| #3 payment | billing-webhook (ЮKassa, Stars), admin (Directus) | #4 subscription, отчёты, админка |
| #4 subscription | subscription-service | gateway-mcp (проверка прав), Keto (policy), бот, Персона flow |
| #5 indicators | Портной-engine | Навигатор, Оркестратор, Диагност, бот (progress bar), Оценщик |
| #6 learning | learning-service, Directus (наставники), scheduler | бот (ленты), Портной (сигналы), Metabase |
| #7 knowledge | knowledge-mcp indexer | gateway-mcp, все агенты (retrieval), content-pipeline |
| #8 reference | admin (Directus) | все сервисы, которые используют константы/тарифы/квалификации |
| #9 publication | content-pipeline | бот (анонсы), аналитика охвата, auth-proxy к каналам |
| #10 community | matching-engine, mentorship-service, referral-tracker, event-organizer | бот (анонсы встреч), Metabase, Навигатор |
| #11 lead | landing, acquisition-funnel | marketing analytics, Директор-УС (CRM), админка |
| #12 rewards | rewards-engine, Методсовет (Directus) | бот (баланс, ачивки), Персона (статус), публикации, Navigator |

---

## 7. Что осталось вне Neon (и почему)

| Не БД Neon | Причина | Где живёт |
|---|---|---|
| **Activity Hub (старый)** | был контейнер-смесь, не домен (Д11: событие ≠ хранилище) | распущен между #2/#3/#9/#11/#12 |
| **Health / Uptime** | SaaS закрывает задачу (Д2) | Better Uptime / Statuspage.io |
| **Metabase** | аналитический инструмент, не домен (Д2) | поверх #1-#12 через read-replica |
| **Ory identity** | внешняя система (Д1) | Ory Kratos, у нас только `ory_id` FK |
| **Ory Keto policies** | внешняя система | Ory Keto, у нас ссылки на relation_tuples |
| **FSM-state бота, message cache** | runtime-состояние, ephemeral | Redis / in-memory |
| **Pack пользователя (PACK-personal)** | Git пользователя, не Neon | GitHub, проекция → #1 |
| **Pack платформы (PACK-digital-platform, PACK-MIM, ZP, FPF, SOTA)** | Git команды, не Neon | GitHub, проекция → #7 |
| **On-demand pull Pack-состояния** | слой «Контекст» WP-257, runtime-сборка | не хранится; кэш → #5 `snapshot` |
| **LMS Aisystant** | legacy внешняя система | отдельная БД LMS, read-only bridge |
| **Langfuse traces** | observability SaaS | Langfuse cloud |
| **WakaTime activity** | 3rd-party tracking | WakaTime cloud, pull → #2 journal (projection) |

---

## 8. Миграция 9 → 12 БД (эстафета в WP-253)

Планируется в **WP-253 (DP.ROADMAP.001)** — мастер-план фаз с gating-критериями. WP-228 Ф25 утверждает целевую карту, WP-253 Ф1 строит фазы.

### 8.1 Таблица переходов

| Текущая БД (9) | Целевая БД (12) | Действие | Зависимость РП |
|---|---|---|---|
| `platform` (#1 в v1) | Персона #1 + Подписка #4 + Справочник #8 | расщепить по writer/owner | WP-227, WP-253 |
| `knowledge` (#2 в v1) | Персона #1 (личные коллекции) + Знание-платформы #7 (платформенные) | расщепить по владельцу | WP-187 |
| `activity-hub` (#3 в v1) | Журнал #2 + Публикации #9 + Лид #11 + Награды #12 | распустить (Д11) | WP-253 |
| `payment-registry` (#4 в v1) | Платёж #3 + Награды #12 (баллы) | расщепить | WP-246 |
| `digital-twin` (#5 в v1) | Показатели #5 | переименовать (v1 → canonical) | WP-227 |
| `aist-bot` (#6 в v1) | Обучение #6 + Журнал #2 (events) + runtime (FSM → Redis) | расщепить по слоям | WP-254 |
| `metabase` (#7 в v1) | — | убрать (Д2, read-replica над другими) | WP-244 |
| `health` (#8 в v1) | — | убрать (Д2, внешний SaaS) | WP-244 |
| `content-pipeline` (#9 в v1) | Публикации #9 | переименовать | WP-155 |
| (новая) | Сообщество #10 | создать (extract из PMB-1 + WP-256) | WP-256 |
| (новая) | Лид #11 | создать (extract из v1 #3/#4) | WP-188 |
| (новая) | Награды #12 | создать (консолидация баллов + ачивок + квалификаций) | WP-253 |

### 8.2 Порядок миграции (ориентировочно)

1. **Подготовка** — DP.ROADMAP.001 (WP-253 Ф1): утвердить целевые имена, проверить занятость имён в Neon, подготовить DDL.
2. **Низкорискованные переименования** — `digital-twin → indicators`, `content-pipeline → publication` (WP-155 / WP-227).
3. **Расщепление `platform`** — вынести Подписку и Справочник в отдельные БД (WP-253 Ф2).
4. **Расщепление `knowledge`** — личные коллекции в #1, платформенные в #7 (WP-187).
5. **Роспуск `activity-hub`** — outbox projections в #2, #9, #11, #12.
6. **Миграция `aist-bot`** — учебные объекты → #6 learning (WP-254), runtime FSM → Redis.
7. **Создание новых БД** — #10 community (WP-256), #11 lead, #12 rewards.
8. **Снятие лишних** — вывести из production `metabase` DB, `health` DB.

### 8.3 Gating-критерии (уточняются в WP-253 Ф1)

Каждая фаза завершается, только если:
- DDL применён без потери данных (`COUNT` до/после совпадают)
- Все зависимые сервисы читают новую БД (env vars + code updated)
- Retention и PII-классификация соблюдены (WP-212 B7.3)
- Roll-back план задокументирован

---

## 9. Верификация (три чеклиста)

### 9.1 Чеклист SPF.SPEC.005 — выбор БД

Применение B.0 + B1-B4 + N1-N5 к каждой из 12 БД. Все клетки должны быть `✅` или `-` (не применимо).

| БД | B.0 категория | B1 единый writer-owner | B2 свой инвариант | B3 blast-radius | B4 свой словарь | N1 noun-area | N2 central entity | N3 no tech markers | N4 RU concept | N5 lowercase |
|---|---|---|---|---|---|---|---|---|---|---|
| #1 persona | Персона ✅ | пользователь ✅ | Git ↔ Neon consistency ✅ | ✅ независима | словарь Персоны ✅ | persona ✅ | ACCOUNT ✅ | ✅ | Персона ✅ | ✅ |
| #2 journal | Память.Observed ✅ | event-gateway ✅ | append-only ✅ | ✅ deletable | события ✅ | journal ✅ | EVENT ✅ | ✅ | Журнал ✅ | ✅ |
| #3 payment | Память.Observed ✅ | billing-webhook ✅ | идемпотентность платежей ✅ | ✅ (queue on outage) | биллинг ✅ | payment ✅ | PAYMENT ✅ | ✅ | Платёж ✅ | ✅ |
| #4 subscription | Память.Observed ✅ | subscription-service ✅ | период-FK к гранту ✅ | ✅ cached JWT | подписка ✅ | subscription ✅ | GRANT ✅ | ✅ | Подписка ✅ | ✅ |
| #5 indicators | Память.Derived ✅ | Портной ✅ | rebuildable из #2 ✅ | ✅ (Портной = single writer) | показатели ✅ | indicators ✅ | INDICATOR ✅ | ✅ | Показатели ✅ | ✅ |
| #6 learning | Память.Observed+Derived ⚠️ (множественные writers) | learning-service + mentors ⚠️ | наставник-ученик целостность ✅ | ✅ degraded OK | обучение ✅ | learning ✅ | ENROLLMENT ✅ | ✅ | Обучение ✅ | ✅ |
| #7 knowledge | Platform-knowledge ✅ | knowledge-mcp indexer ✅ | Git ↔ эмбеддинг consistency ✅ | ✅ stale OK | онтология ✅ | knowledge ✅ | CONCEPT ✅ | ✅ | Знание-платформы ⚠️ (составное имя) | ✅ |
| #8 reference | Catalog ✅ | admin (Directus) ✅ | temporal validity ✅ | ✅ read-heavy | справочник ✅ | reference ✅ | PROGRAM/TARIFF ✅ | ✅ | Справочник ✅ | ✅ |
| #9 publication | Память.Observed ✅ | content-pipeline ✅ | pipeline draft→article→post ✅ | ✅ retry safe | публикации ✅ | publication ✅ | POST ✅ | ✅ | Публикации ✅ | ✅ |
| #10 community | Relational ✅ | matching+mentorship+events ⚠️ (множественные) | симметричность связей ✅ | ✅ degraded OK | сообщество ✅ | community ✅ | MENTORSHIP/MATCH ✅ | ✅ | Сообщество ✅ | ✅ |
| #11 lead | Proto-Persona ✅ | landing+funnel ✅ | pre-Ory identity ✅ | ✅ standalone | лид ✅ | lead ✅ | LEAD ✅ | ✅ | Лид ✅ | ✅ |
| #12 rewards | Память.Observed+Derived ⚠️ | rewards-engine + Методсовет ⚠️ | баланс = Σ начислений ✅ | ✅ (stale OK) | награды ✅ | rewards ✅ | POINTS_GRANT ✅ | ✅ | Награды ✅ | ✅ |

**Замечания:**
- **#6, #10, #12 — множественные writers.** По B1 строго требуется один семантический writer. Здесь принято сознательное решение: `#6 learning` имеет один BC «Learning Progress», внутри которого writer'ы делят разные аспекты (learner пишет ANSWER, mentor — REVIEW, scheduler — FEED_WEEK); аналогично `#10` и `#12`. Альтернатива — расщеплять каждый на 2-3 БД — признана преждевременной (Post-MVP decision).
- **#7 N4 составное имя** — «Знание-платформы» через дефис. N5 lowercase `knowledge` — ок. Альтернатива «Онтология» отвергнута (размывает: онтология включает и личную).

### 9.2 Замечания Андрея Д1-Д12 (Ф24 ревью 21 апр)

| # | Замечание | Где учтено в 12 БД |
|---|---|---|
| **Д1** | Очистить ядро Platform Core: Созидатель + права доступа (Ory — внешняя) | #1 persona = только декларация + preferences; Ory = `ory_id` FK, никаких собственных identity-таблиц |
| **Д2** | Убрать Health и Metabase из Neon | §7 — выведены на SaaS / read-replica |
| **Д3** | Субагент-исследование кода LMS Димы | §3.13 LMS ERD + bridge-контракт в §8 |
| **Д4** | Payment: плательщик, платёж, начисление, получатель, тарифы, методы оплаты + баллы | #3 payment (плательщик/платёж/метод/чек) + #12 rewards (баллы) + #8 reference (тарифы) + #4 subscription (получатель через грант). Расщеплено вместо одной «перегруженной» payment-registry |
| **Д5** | Knowledge универсальный: коллекция/документ/chunk отделены от concept/мем | #7 knowledge ERD: `COLLECTION → DOCUMENT → PARAGRAPH → EMBEDDING` отделено от `CONCEPT ↔ RELATION`. Личные коллекции — в #1 persona с той же структурой |
| **Д6** | Digital Twin: +пользователь, +квалификация, проверить JSON-модель | #5 indicators с явным `account_id` FK на все замеры; квалификация вынесена в #12 (по HD: квалификация — формальное присвоение, не замер) |
| **Д7+Д8+Д9** | Связи между ОБЪЕКТАМИ (не кластерами) + кратности + названия | §2 карта — все межкластерные стрелки object→object с кратностями 1:1/1:M/M:1/M:M и подписями; §4 таблица связей |
| **Д10** | Ревизия «объект ≠ атрибут» (множитель квалификации, уровень и т.п.) | §3 ERD — все ранее-ошибочные узлы свёрнуты в атрибуты (множитель — колонка `factor` в #8.MULTIPLIER, текущий уровень — derived в #12.QUALIFICATION_CURRENT) |
| **Д11** | Activity Hub: базовый объект «событие» | `activity-hub` БД распущен; central object «СОБЫТИЕ» живёт в #2 journal, узкие типы событий — в #9/#11/#12 outbox-проекциями |
| **Д12** | Созвон с Димой: Payment + LMS (какие объекты нужно отразить) | §3.13 LMS ERD отражает 10+ физ.объектов LMS; WP-228 Ф24 lms-audit — отдельный документ. Bridge-чекпоинт в §8 |

**Все 12 правок Андрея учтены.**

### 9.3 Полнота по категориям WP-257

| Категория WP-257 | Покрыто | Комментарий |
|---|---|---|
| **Персона** (writer=пользователь, owner=Git) | ✅ #1 persona | Центральная БД пользователя; Git-проекция индексируется в Neon |
| **Память.Observed** (writer=платформа, event-based) | ✅ #2 #3 #4 #9 | Журнал как универсальный event-stream; домен-специфичные события в #3/#4/#9 |
| **Память.Derived** (writer=engine, расчётный) | ✅ #5 | Портной владеет всеми расчётами (RCS, baseline, potential, индикаторы) |
| **Память.Observed+Derived** (смешанные writers) | ⚠️ #6 #12 | #6 learning и #12 rewards имеют наблюдаемые (ответ, платёж) и расчётные (оценка, квалификация) таблицы вместе — сознательное решение, BC един |
| **Platform-knowledge** (проекция онтологии) | ✅ #7 | knowledge-mcp индексирует платформенные Pack; личные — в #1 |
| **Catalog/Reference** (writer=admin) | ✅ #8 | Тарифы, ступени, уровни квалификации, правила |
| **Relational** (связи Persona↔Persona) | ✅ #10 | Полный цикл: matching → поддержка → события → развитие |
| **Proto-Persona** (до Ory) | ✅ #11 | Лид → claim → Персона |
| **Service/Ops** | ❌ 0 БД | Вынесено на SaaS (Д2) |
| **Контекст** (runtime, не хранится) | ❌ не БД | Runtime промпт-сборка, кэш on-demand → #5 SNAPSHOT |

**Покрытие полное за вычетом Service/Ops (сознательный отказ Д2) и Контекст (не storage-категория).**

### 9.4 Применённые различения (проверка)

| Различение (HD / distinctions.md) | Где применено в архитектуре |
|---|---|
| Персона ≠ Память ≠ Контекст (DP.D.052, HD #27) | §1 сводная таблица — категория WP-257 для каждой БД |
| Событие ≠ Хранилище-контейнер (Р-W17-1, Д11) | Activity Hub распущен (§8), `#2 journal.EVENT` — единственное место всех событий |
| Объект ≠ Атрибут (Д10) | Все ERD в §3 — множители/коды/lookup как атрибуты, не узлы |
| Система ≠ Роль (D.141 PD) | `#1.ACCOUNT` = система; mentorship/ambassador в `#10` = роли через связи |
| Характеристика ≠ Показатель ≠ Значение ≠ Потенциал (D.140) | `#5 indicators` — `MEASUREMENT` (значение-сейчас) + `BASELINE` (устойчивое значение) + `POTENTIAL` + `INDICATOR` (композит); `CHARACTERISTIC` определяется в Pack, не в БД |
| RCS-ступень ≠ Уровень квалификации | `#5.RCS_CURRENT` (derived Портным, 5 ступеней) vs `#12.QUALIFICATION_CURRENT` (formal Методсовет, 11 уровней) |
| Объект ≠ Отношение (WP-228 19 апр, Andrey) | ERD показывают только объекты с именами собственными; служебные M:N без атрибутов — на физ.схеме |
| PII ≠ payment_credentials | `#3.PAYMENT_METHOD.masked_id` явно помечен `payment_credentials` — строже PII |
| Рабочий документ ≠ Публикация | `#1.NOTES` + `#1.CAPTURES` (черновое) vs `#9.PUBLICATION_EVENT` (факт публикации) |
| Бесплатный Gateway ≠ Полный Gateway (DP.SC.112) | #1 персональные коллекции доступны подписчикам; #7 платформенные — всем аутентифицированным |
| ER ≠ Физ.схема | §3 — концептуальная ER; физ.схема с индексами/партициями — в WP-253 Ф2 |

**Все ключевые различения применены.**

---

## 10. Открытые вопросы

### 10.1. Расщепление #6/#10/#12 (множественные writers)

B1 проход `⚠️` у трёх БД. Вопрос: нужен ли follow-up РП по строгой ревизии после MVP? Кандидат: **WP-258-post-mvp-db-split** (Q3 2026) — проанализировать, оправдано ли совмещение писателей, или BC надо расщеплять.

### 10.2. `#7 knowledge` N4 составное имя

«Знание-платформы» через дефис — не идеально по N4 (single-word в русском). Альтернатива «Онтология» отвергнута (включает личную). Текущее решение — записать в name_rationale: «двусоставное имя допустимо, когда русский single-word создаёт онтологический конфликт».

### 10.3. Пакетное переименование БД в prod

Не раньше DONE зависимых WP (WP-155, WP-246, WP-187, WP-254). Требует: DDL migration + env vars update + MCP tool names + bot handlers update. Эстафета — WP-253.

### 10.4. Сервисы Persona / Memory / Projection

Pack-сущности определены (DP.ARCH.005 Persona Entity, DP.ARCH.006 Memory Record, DP.ARCH.007 Projection — WP-257 Ф5, 22 апр 2026, заменили монолитный DP.ARCH.003). Выделение соответствующих runtime-сервисов (persona-mcp / memory-mcp / projection-service) — открытый вопрос реализации, решается в WP-253 мастер-плане по БД + будущих ArchGate.

### 10.5. Retention / GDPR политики

Временные окна для append-only таблиц (`#2.EVENT`, `#9.PUBLICATION_EVENT`, `#11.FUNNEL_EVENT`) не зафиксированы. WP-240 Retention policy — уточнить в Q2.

### 10.6. Plugin API L2 (WP-258)

Как внешние разработчики расширяют Персону и Показатели, не трогая L2 ядро? Design в Q3 (WP-258 из WP-250 Ф-F.4).

### 10.7. Контракт bridges с LMS

Формализовать: какие таблицы LMS read-only bridge vs писатель-мигратор. Фиксация в будущем `DP.ARCH.008-lms-bridge-contracts.md` (WP-254 Ф0; ID сдвинут — DP.ARCH.005/006/007 заняты под Persona/Memory/Projection в WP-257 Ф5).

---

## 11. Связанные артефакты

- **Карта данных (операционный трекер):** `DS-my-strategy/inbox/WP-228-neon-data-map.md`
- **Мастер-план миграции:** `PACK-digital-platform/.../DP.ROADMAP.001` (в WP-253 Ф1)
- **Аудит LMS:** `DS-my-strategy/inbox/WP-228-F24-lms-audit.md`
- **Правила границ:** `SPF/spec/SPF.SPEC.005-boundary-rules.md`
- **Метод ER-моделирования:** `PACK-digital-platform/.../DP.METHOD.040-er-modeling.md`
- **Канон Персона/Память/Контекст:** `memory/project_persona_memory_context.md` (DP.D.052)
- **Классификация данных (B7.3):** WP-212 B7.3.1 L2 Data Classification Map (в работе)

---

## 12. История версий

| Версия | Дата | Описание |
|---|---|---|
| v1 | 14 апр 2026 | Первая версия: 9 БД, монолитный «ЦД» в `digital-twin`, `activity-hub` как контейнер-смесь |
| v1.1 | 19 апр 2026 | +`content-pipeline` БД (9-я база); §7.0.2 реестр физ.объектов |
| v1.2 | 21 апр 2026 | Ф24 правки Андрея (Д1-Д12): очистка Platform Core, вынос Metabase/Health, роспуск Activity Hub подготовлен |
| **v2** | **22 апр 2026** | **Целевая карта 12 БД (Ф25): расщепление ЦД → Персона/Память/Контекст (WP-257); новые БД Сообщество/Лид/Награды; вынос SaaS; верификация по 3 чеклистам** |
