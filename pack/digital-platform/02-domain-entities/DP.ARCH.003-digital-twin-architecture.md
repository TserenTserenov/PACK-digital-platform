---
id: DP.ARCH.003
name: Архитектура цифрового двойника (3-слойная)
type: domain-entity
status: approved
summary: "3-слойная архитектура ЦД: Events → State → Views. Event Sourcing + CQRS. 5 schemas Neon (public, development, finance, connections, operations). Безопасность: RLS + 5 ролей PostgreSQL. Эволюция: 5 начальных проекций → CHR-Space (30+ измерений)"
created: 2026-02-24
updated: 2026-03-11
trust:
  F: 4
  G: domain
  R: 0.6
epistemic_stage: emerging
related:
  uses: [DP.ARCH.001, DP.ARCH.002, DP.CONCEPT.001, DP.SOTA.009]
  enables: [DP.AISYS.014]
tags: [digital-twin, event-sourcing, cqrs, learner-model, knowledge-tracing]
---

# Архитектура цифрового двойника (3-слойная)

## 1. Назначение

Документ описывает архитектуру **цифрового двойника созидателя** — вычислительной модели, которая отражает текущее состояние пользователя и обновляется из каждого его действия на платформе.

**Ключевой принцип: ни одно действие пользователя не проходит мимо.** Каждое взаимодействие (ответ на тест, чат с ИИ, выполнение ДЗ, рефлексия, марафон) записывается как событие и влияет на профиль.

---

## 2. Проблемы, которые решает

| # | Проблема | Как решает |
|---|---------|------------|
| 1 | Действия пользователя пропадают — нет единого лога | Слой Events: append-only лог ВСЕХ действий из ВСЕХ систем |
| 2 | Невозможно вычислить профиль — нет первичных данных | State считается из Events по определённым методам (BKT, HLR, агрегаты) |
| 3 | Нельзя добавить новую аналитику ретроспективно | Event Sourcing: replay событий → новая проекция заполняется за всё время |
| 4 | Профиль «заморожен» — не учитывает забывание | Half-Life Regression моделирует затухание навыков |
| 5 | Невозможно ответить «что знал пользователь месяц назад?» | Temporal queries по event store |
| 6 | Разные системы (бот, LMS, Claude Code) не связаны | Единый event store — единая точка сбора из всех источников |

---

## 3. Три слоя архитектуры

```
┌─────────────────────────────────────────────────────────────┐
│  СЛОЙ 1: EVENTS (неизменяемый лог)                          │
│                                                             │
│  Каждое действие пользователя = событие                     │
│  Append-only. Никогда не удаляется, не изменяется.          │
│                                                             │
│  Источники: Бот, LMS, Claude Code, MCP, Стратег            │
└───────────────────────────┬─────────────────────────────────┘
                            │ on_event() / rebuild()
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  СЛОЙ 2: STATE (проекции = текущий профиль)                 │
│                                                             │
│  N независимых проекций из одного потока событий:           │
│  ├── skill_mastery    (BKT → P(known) по навыкам)           │
│  ├── memory_decay     (HLR → полураспад, spaced repetition) │
│  ├── engagement       (агрегаты → streak, regularity)       │
│  ├── misconceptions   (паттерн-матчинг + LLM extraction)    │
│  └── qualifications   (threshold rules → стадия, индексы)   │
│                                                             │
│  Каждая проекция пересчитывается из событий.                │
│  Можно построить N вариантов для одного пользователя.       │
└───────────────────────────┬─────────────────────────────────┘
                            │ по запросу
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  СЛОЙ 3: VIEWS (генерируемые, не хранятся)                  │
│                                                             │
│  Рекомендации: «что учить следующим» (bottleneck-first)     │
│  Прогнозы: «когда навык протухнет без повторения»           │
│  Отчёты: недельный/месячный прогресс                        │
│  Сравнения: профиль vs когорта                              │
│  Open Learner Model: визуализация профиля для пользователя  │
└─────────────────────────────────────────────────────────────┘
```

### Принцип: Events → N проекций

```
                         ┌── Проекция A: skill_mastery (BKT)
                         │
Events (один поток) ─────┼── Проекция B: engagement_profile (агрегаты)
                         │
                         ├── Проекция C: misconception_map (LLM)
                         │
                         ├── Проекция D: memory_decay (HLR)
                         │
                         └── Проекция E: qualifications (threshold rules)
```

Проекции A–D **независимы** — считаются из событий напрямую.
Проекция E — **зависимая** — агрегирует результаты A–D.

### Почему 3 слоя, а не 4 (как было)

Предыдущая метамодель делила данные на 4 слоя по **источнику** (кто пишет): declarative, collected, derived, generated. Это создавало ложные архитектурные границы:

- «Declarative» (самооценка) и «Collected» (тесты) — оба являются **событиями**, различаясь лишь атрибутом `confidence`
- «Derived» и «Generated» — оба являются **вычислениями из событий**, различаясь лишь тем, хранится ли результат

**Новая модель делит по природе данных:**

| Слой | Природа | Ключевой вопрос | Хранится? |
|------|---------|-----------------|-----------|
| Events | Неизменяемые факты | «Что произошло?» | Да, навсегда |
| State | Вычисляемый снимок | «Кто этот пользователь сейчас?» | Да (snapshot) |
| Views | Генерируемые ответы | «Что делать дальше?» | Нет |

---

## 4. Слой Events: структура и типы

### 4.1 Схема события

Каждое событие содержит:

| Поле | Тип | Описание |
|------|-----|----------|
| `id` | BIGSERIAL | Уникальный ID (монотонно растущий) |
| `user_id` | BIGINT | Пользователь |
| `event_type` | TEXT | Тип события (см. каталог ниже) |
| `source` | TEXT | Система-источник: `bot`, `lms`, `claude_code`, `strategist` |
| `payload` | JSONB | Специфичные данные события |
| `confidence` | FLOAT | Доверие к сигналу: 0.0–1.0 |
| `skill_ids` | TEXT[] | Затронутые навыки (если применимо) |
| `created_at` | TIMESTAMPTZ | Время события |

### 4.2 Каталог типов событий

#### Прямые действия (confidence = high)

| event_type | source | payload (ключевые поля) | Что даёт |
|------------|--------|------------------------|----------|
| `test_answered` | bot, lms | `questionId, skillId, depth, correct, responseMs` | Skill mastery (BKT) |
| `homework_submitted` | lms | `homeworkId, skillIds[], score, timeSpentMs` | Mastery + engagement |
| `marathon_step` | bot | `lessonId, stepId, completed, timeSpentMs` | Progress, time-on-task |
| `feed_answered` | bot | `questionId, answer, correct` | Knowledge signals |
| `session_start` | bot | `platform, timestamp` | Streak, regularity |
| `session_end` | bot | `durationMs` | Session length |

#### Декларации пользователя (confidence = lower, субъективные)

| event_type | source | payload | Что даёт |
|------------|--------|---------|----------|
| `goal_set` | bot | `goal, targetSkill, targetDepth` | Цели для gap-анализа |
| `self_assessed` | bot | `skillId, selfRating` | Субъективная оценка (калибровка с объективной) |
| `profile_updated` | bot | `field, oldValue, newValue` | Контекст |
| `reflection` | bot | `text, extractedInsights[]` | Metacognition |
| `note_created` | bot | `text, tags[]` | Рефлексивность |

#### Системные (confidence = high, автоматические)

| event_type | source | payload | Что даёт |
|------------|--------|---------|----------|
| `ai_chat` | bot | `sessionId, userMessage, extractedSkills[], comprehension` | Misconceptions, interests |
| `strategy_session` | strategist | `weekPlan, rpCount` | Инициативность |
| `knowledge_extracted` | extractor | `entities[], packId` | Вклад в знания |

### 4.3 Confidence как атрибут, не слой

Бывшее деление «declarative vs collected» заменяется **атрибутом confidence**:

| Тип сигнала | confidence | Пример |
|-------------|-----------|--------|
| Прямой тест / ДЗ | 0.9–1.0 | Правильный/неправильный ответ |
| Поведенческий | 0.7–0.9 | Session duration, streak |
| LLM-извлечённый | 0.4–0.7 | Misconception из чата |
| Самооценка | 0.3–0.5 | «Я знаю ZP.1 на 4 из 5» |

При Bayesian update: `confidence` используется как вес — тестовый ответ двигает P(known) сильнее, чем косвенный сигнал из чата.

---

## 5. Слой State: проекции и методы вычисления

### 5.1 Проекция: Skill Mastery

**Вопрос:** «Насколько пользователь владеет каждым навыком?»

**Метод: Bayesian Knowledge Tracing (BKT)**

Для каждой пары (user, skill) поддерживаем P(known) — вероятность владения навыком.

```
Параметры BKT (на навык):
  P(L₀)  = начальное знание (prior)                    ≈ 0.1
  P(T)   = вероятность выучить за 1 попытку             ≈ 0.3
  P(G)   = вероятность угадать правильный ответ         ≈ 0.25
  P(S)   = вероятность ошибиться по невнимательности    ≈ 0.1

При правильном ответе:
  P(L|correct) = P(L)×(1−P(S)) / [P(L)×(1−P(S)) + (1−P(L))×P(G)]

При неправильном ответе:
  P(L|wrong)   = P(L)×P(S) / [P(L)×P(S) + (1−P(L))×(1−P(G))]

После обновления — шанс обучения:
  P(L_new) = P(L_updated) + (1 − P(L_updated)) × P(T)
```

**Входные события:** `test_answered`, `homework_submitted`, `feed_answered`

**Выход:** `{user_id, skill_id, p_known, bloom_depth, attempts, last_event}`

**Маппинг Bloom depth:** P(known) → Bloom через пороги:

| P(known) | Bloom depth | Интерпретация |
|----------|-------------|---------------|
| 0.0–0.2 | 0 (не знает) | Нет владения |
| 0.2–0.4 | 1 (запоминание) | Может вспомнить |
| 0.4–0.6 | 2 (понимание) | Может объяснить |
| 0.6–0.75 | 3 (применение) | Может применить |
| 0.75–0.9 | 4 (анализ/оценка) | Может проанализировать |
| 0.9–1.0 | 5 (создание) | Может создать новое |

**SOTA эволюция:** BKT (v1, сейчас) → BKTransformer (v2, при >100K событий) → Deep KT + Cognitive Load (v3).

### 5.2 Проекция: Memory Decay

**Вопрос:** «Когда навык "протухнет" без повторения?»

**Метод: Half-Life Regression (HLR, Duolingo)**

```
P(recall, t) = 2^(−Δt / h)

h = 2^(θ · x)

где x = [кол-во повторений, кол-во правильных, кол-во ошибок,
         текущий P(known), время с последнего повторения]
```

**Что даёт:**
- Для каждого навыка: `next_review_date` — когда P(recall) упадёт ниже 0.5
- Spaced repetition: бот знает, когда напомнить пользователю повторить навык
- Предиктивный ЦД: «через 2 недели без практики ZP.3 деградирует»

**Входные события:** те же, что для Skill Mastery

**Выход:** `{user_id, skill_id, half_life, p_recall_now, next_review_date}`

### 5.3 Проекция: Engagement Profile

**Вопрос:** «Насколько регулярно и интенсивно пользователь занимается?»

**Метод: скользящие агрегаты**

| Метрика | Формула |
|---------|---------|
| `streak_days` | Longest consecutive days with ≥1 event |
| `active_days_7d` | `COUNT(DISTINCT date) WHERE last 7 days` |
| `active_days_30d` | `COUNT(DISTINCT date) WHERE last 30 days` |
| `regularity` | `active_days_7d / 7` |
| `avg_session_min` | `AVG(session_duration) WHERE last 30 days` |
| `preferred_hour` | `MODE(EXTRACT(hour FROM created_at))` |
| `events_per_active_day` | `COUNT(events) / active_days_30d` |

**Входные события:** все типы (по факту наличия)

**Выход:** `{user_id, streak, regularity, avg_session_min, preferred_hour, events_per_active_day}`

### 5.4 Проекция: Misconception Map

**Вопрос:** «Какие конкретные заблуждения у пользователя?»

**Метод: паттерн-матчинг + LLM extraction (async)**

Для тестов:
- Неправильный ответ → маппинг на `common_errors` из ячеек curriculum (EDU.MAP.001)
- Каждая ячейка содержит типичные ошибки → автоматический матчинг

Для чатов:
- Async LLM (Haiku) анализирует сообщения пользователя
- Экстрагирует misconceptions с confidence score

**Входные события:** `test_answered` (неправильные), `ai_chat`

**Выход:** `{user_id, skill_id, misconception_type, evidence_count, confidence, first_seen, last_seen}`

### 5.5 Проекция: Qualifications (стадия и квалификация)

**Вопрос:** «На какой стадии развития находится пользователь?»

**Метод: threshold rules — пороговые правила поверх других проекций**

Это единственная проекция, которая читает другие проекции, а не сырые события:

```
                   Skill Mastery ──┐
                   Engagement    ──┼──→ Qualifications (threshold rules)
                   Misconceptions──┘
```

Пороги определяются для каждой стадии (Random → Practicing → Systematic → Disciplined → Proactive) через комбинацию: min(P(known)) по ZP-навыкам, active_days, misconception_count.

**Выход:** `{user_id, stage, agency_index, mastery_index, worldview_level}`

---

## 6. Три типа характеристик по вычислимости

Не все характеристики созидателя одинаково вычислимы из событий.

| Тип | Описание | Confidence | Примеры |
|-----|----------|-----------|---------|
| **A. Прямо вычислимые** | Считаются из событий по формуле | Высокий | Streak, часы в неделю, P(known), recovery speed |
| **B. Косвенно выводимые** | Proxy-сигналы через поведение или LLM | Средний | Эмоциональная устойчивость (через variance активности), misconceptions (через LLM) |
| **C. Только декларация** | Пользователь сообщает сам, объективно не проверить | Низкий | Мемы, калибр личности, контроль инфопотока |

### Пример вычисления характеристики типа B: «Устойчивость к сбоям»

```yaml
characteristic: "Устойчивость к сбоям"
computation_type: A (прямо из событий)
input_events: [session_start]
method: |
  1. Найти все gaps > 3 дней (нет событий)
  2. Для каждого gap: дней до первого дня с ≥ 2 событиями
  3. recovery_speed = median(recovery_days)
thresholds:
  good: < 2 дней
  medium: 2–5 дней
  poor: > 5 дней
confidence: high
```

### Пример вычисления характеристики типа B: «Эмоциональная стабильность» (proxy)

```yaml
characteristic: "Эмоциональная стабильность (proxy)"
computation_type: B (косвенно)
input_signals:
  - regularity_variance (дисперсия active_days за 30 дней) — тип A
  - recovery_speed (дней до восстановления streak) — тип A
  - session_time_variance (стабильность длины сессий) — тип A
  - sentiment_trend (тренд тональности в чатах) — тип B, LLM
  - self_reported_state (самооценка состояния) — тип C
method: |
  stability_proxy = w1 × (1 − norm(regularity_variance))
                  + w2 × (1 − norm(recovery_speed))
                  + w3 × (1 − norm(session_time_variance))
                  + w4 × sentiment_score
                  + w5 × self_reported_state
  где w1-w5 — веса (начальные: 0.25, 0.25, 0.2, 0.15, 0.15)
confidence: medium
caveat: "Proxy ≠ клиническая оценка. Показывать пользователю с пометкой."
```

**Правило:** для каждой характеристики из метамодели ЦД нужна такая карточка метода. Характеристики без карточки — не вычислимы.

---

## 7. Как State участвует в работе платформы

### 7.1 Бот (DP.AISYS.014)

| Сценарий | Какие данные State использует | Что делает |
|----------|------------------------------|-----------|
| Консультация «?» | skill_mastery + misconceptions | Адаптирует глубину объяснения под P(known) пользователя |
| Марафон | engagement + skill_mastery | Подбирает следующий урок по bottleneck-first |
| Лента | skill_mastery + memory_decay | Вопросы по навыкам с P(recall) < 0.5 (spaced repetition) |
| /mydata | Все проекции | Показывает Open Learner Model пользователю |
| Тест принципов | skill_mastery + misconceptions | Адаптивный тест: начинает с P(known)≈0.5, двигается вверх/вниз |

### 7.2 ИИ-агенты

| Агент | Какие данные | Что делает |
|-------|-------------|-----------|
| Стратег | qualifications + engagement | Учитывает стадию при планировании недели |
| Проводник (Консультант) | skill_mastery + misconceptions | Адаптирует промпт под профиль |
| Диагност (EDU.R.006) | skill_mastery + engagement | `priority(P) = gap(P) × impact(P)` — рекомендует фокус |

### 7.3 Пользователь (Open Learner Model)

Пользователь видит свой профиль через бота (/mydata) или веб:

```
Твой профиль принципов:
  ZP.1 Аксиоматичность: ████████░░ 78% (глубина 3: Применение)
  ZP.2 Структура:       ████░░░░░░ 42% (глубина 2: Понимание)
  ZP.3 Многомасштабность:██░░░░░░░░ 23% (глубина 1: Запоминание)  ← bottleneck
  ...

Рекомендация: сфокусируйся на ZP.3 — это ограничивает ZP.4 и FPF.Holon

Streak: 12 дней | Регулярность: 5/7 дней | Сессия: ~25 мин
Навыки для повторения: ZP.1 (через 3 дня протухнет)
```

### 7.4 Замыкание цикла: Events → State → ИИ → новые Events

```
Пользователь отвечает на вопрос
        ↓
Event: test_answered {skill: ZP.3, correct: false}
        ↓
State update: P(known ZP.3) = 0.23 → 0.19
        ↓
View: Диагност видит bottleneck
        ↓
Бот предлагает материал по ZP.3 (ячейка из curriculum)
        ↓
Пользователь изучает и отвечает на новый вопрос
        ↓
Event: test_answered {skill: ZP.3, correct: true}
        ↓
State update: P(known ZP.3) = 0.19 → 0.35
        ↓
... цикл продолжается
```

Это реализация **Intervention Loop** (DP.M.007): действие → измерение → обновление модели.

---

## 8. Сравнение с предыдущей метамоделью (4 слоя)

| Аспект | Было (4 слоя) | Стало (3 слоя) |
|--------|---------------|----------------|
| Принцип деления | По источнику (кто пишет) | По природе данных (что это) |
| Слои | declarative, collected, derived, generated | Events, State, Views |
| Сырые события | Не хранились | Append-only лог (навсегда) |
| Ретроспективные запросы | Невозможны | `SELECT * FROM events WHERE user_id=X AND created_at < '2026-01-15'` |
| Новая аналитика | Нужно новое поле | Replay событий → новая проекция |
| Забывание | Не моделировалось | HLR: P(recall) затухает со временем |
| Несколько вариантов профиля | Невозможно | N проекций из одного потока |
| Самооценка vs тест | Разные слои (1 vs 2) | Одно и то же событие, разный `confidence` |

---

## 9. АрхГейт

| Характеристика | Оценка | Обоснование |
|---|---|---|
| **Эволюционируемость** | 9 | Новый тип события = новый producer, без изменения остальных слоёв. Новая проекция = новый consumer + replay. Event store — самая эволюционируемая архитектура для данных |
| **Масштабируемость** | 9 | Events партиционируются по user_id. Проекции — независимые read-модели. Neon serverless. Snapshotting каждые 100 событий или еженедельно |
| **Обучаемость** | 8 | 3 слоя проще 4. «Действие → событие → профиль» — 2 минуты объяснения. BKT — формула из 4 параметров |
| **Генеративность** | 9 | Ретроспективные запросы. A/B проекций. Open Learner Model. Работает в шаблоне экзокортекса |
| **Скорость** | 8 | Запись: <10ms (append). Чтение snapshot: <50ms. BKT update: <1ms. Генерация View: 2–4 сек (Claude API) |
| **Современность** | 9 | Event Sourcing + CQRS — production-proven. xAPI — IEEE 2023. BKT+HLR — Duolingo production. BKTransformer — SOTA 2025 |
| **Сумма** | **52/60 (8.7)** | **Проходит АрхГейт (порог ≥8)** |

---

## 10. Текущие ИТ-системы и план внедрения

### 10.1 Что есть сейчас

| Система | Статус | Какие события генерирует |
|---------|--------|------------------------|
| **AIST Bot** (@aist_me_bot) | Production, Railway + Neon | Чат, марафон, лента, заметки, тест, настройки ЦД |
| **LMS** | Внешняя (Aisystant) | ДЗ, прохождение курсов, тесты |
| **MCP knowledge-mcp** | Production, CF Workers | Поиск знаний (косвенно — через бота) |
| **MCP digital-twin** | Production, CF Workers | Чтение/запись ЦД |
| **Neon DB** | Production | Профиль, сессии, QA-история, заметки, лента, марафон |
| **Claude Code** (T3–T4) | Локальный | Сессии, captures, commits |

### 10.2 Что нужно сделать

#### Фаза 0: Event Store (минимальная, 1 день)

| # | Задача | Бюджет | Результат |
|---|--------|--------|-----------|
| 1 | Создать таблицу `user_events` в Neon | 30 мин | Хранилище событий |
| 2 | Функция `log_event()` в боте | 1h | Единая точка записи |
| 3 | Подключить P0-точки: session_start, ai_chat, marathon_step | 2–3h | Начало сбора |
| 4 | Простая проекция engagement (SQL view) | 1h | Первые метрики |

**После Фазы 0:** каждое действие в боте записывается. Через неделю — данные для анализа.

#### Фаза 1: Skill Mastery (зависит от WP-55, ~1 неделя)

| # | Задача | Бюджет | Зависимость |
|---|--------|--------|-------------|
| 5 | Ячейки curriculum со skill_ids (WP-55) | 4–6h | — |
| 6 | Тестовые вопросы, привязанные к skill_ids | 2–3h | #5 |
| 7 | BKT-модуль: обновление P(known) при ответе | 2–3h | #3, #6 |
| 8 | Проекция skill_mastery в Neon | 1h | #7 |

**После Фазы 1:** бот знает P(known) по каждому принципу для каждого пользователя.

#### Фаза 2: Memory + Recommendations (~2 недели)

| # | Задача | Бюджет | Зависимость |
|---|--------|--------|-------------|
| 9 | HLR-модуль: half-life, next_review_date | 2–3h | #8 |
| 10 | Spaced repetition в ленте: вопросы по «протухающим» навыкам | 2–3h | #9 |
| 11 | Open Learner Model: /mydata показывает вектор навыков | 2–3h | #8 |
| 12 | Bottleneck рекомендация: «что учить» | 1–2h | #8 |

#### Фаза 3: LLM Extraction + Misconceptions (позже)

| # | Задача | Бюджет |
|---|--------|--------|
| 13 | Async Haiku extraction из чатов | 3–4h |
| 14 | Misconception map | 2–3h |
| 15 | Карточки методов для всех характеристик (тип A, B, C) | 3–4h |
| 16 | Qualifications проекция (стадии) | 2–3h |

#### Фаза 4: LMS интеграция (когда будет API)

| # | Задача | Бюджет |
|---|--------|--------|
| 17 | Webhook/API для приёма событий из LMS | 2–3h |
| 18 | Маппинг LMS-курсов на skill_ids | 2–3h |

---

## 11. SOTA-основа

| Технология / подход | Источник | Как используем |
|---------------------|----------|----------------|
| **Event Sourcing + CQRS** | Microsoft Azure Patterns, production-proven | Архитектура слоёв Events → State |
| **xAPI (Actor-Verb-Object)** | IEEE Standard (Oct 2023) | Формат событий, совместимость |
| **Bayesian Knowledge Tracing** | Corbett & Anderson 1995, тысячи публикаций | Skill mastery P(known) |
| **Half-Life Regression** | Duolingo (Settles 2016), production 1B+ exercises/day | Memory decay, spaced repetition |
| **BKTransformer** | JEDM 2025 | Эволюция BKT → hybrid (v2) |
| **Open Learner Model** | IEEE TLT 2020 | Видимый профиль для пользователя |
| **Knowledge-Based Digital Twins** | DP.SOTA.009, Personal.ai, Lenovo Qira | Концептуальная рамка |
| **Overlay + Perturbation Model** | PMC 2022 | Skill mastery + misconception map |
| **Duolingo Birdbrain** | Duolingo Engineering Blog | ELO-подобная система ability+difficulty |
| **Khan Academy Mastery** | EDM 2022 | 4-уровневый state machine per skill |

---

## 12. Связанные документы

- [DP.ARCH.001](DP.ARCH.001-platform-architecture.md) — 3-слойная архитектура платформы
- [DP.ARCH.002](DP.ARCH.002-service-tiers.md) — Тиры обслуживания (что пользователь получает на каждом тире)
- [DP.CONCEPT.001](DP.CONCEPT.001-platform-concept.md) — Концепция платформы
- [DP.SOTA.009](../06-sota/DP.SOTA.009-knowledge-digital-twins.md) — Knowledge-Based Digital Twins
- [DP.M.007](../03-methods/DP.M.007-intervention-loop.md) — Intervention Loop
- [EDU.FORM.006](../../education/02-domain-entities/formalizations/EDU.FORM.006-thinking-profile-model.md) — Модель профиля мышления (вектор Bloom)
- [EDU.R.006](../../education/02-domain-entities/02A-roles.md#edu-r-006) — Диагност узких мест (bottleneck formula)
- [EDU.MAP.001](../../education/07-map/EDU.MAP.001-competency-depth-cell-template.md) — Шаблон ячеек (skill_ids)

---

## 13. Хранилище: 5 schemas Neon

**Принцип: ЦД = ВСЯ база данных целиком**, а не отдельная schema. Каждая schema отвечает за свой домен, но вместе они составляют полный ЦД созидателя.

```
database (ОДНА)
│
├── schema: public                      [Идентичность пользователя]
│   └── users                           ← Канонический профиль (id, telegram_id, email,
│                                          lms_id, ory_id, name, language, current_tier)
│
├── schema: development                 [Развитие: события + проекции]
│   ├── user_events                     ← Events (append-only, PARTITIONED BY RANGE)
│   ├── skill_mastery                   ← State projection (BKT)
│   ├── memory_decay                    ← State projection (HLR)
│   ├── engagement                      ← State projection (агрегаты)
│   ├── misconceptions                  ← State projection (LLM)
│   └── qualifications                  ← State projection (threshold rules)
│
├── schema: finance                     [Финансы, токены, роялти]
│   ├── token_transactions / balances   ← Лог + баланс токенов
│   ├── subscriptions / payments        ← Подписки и платежи
│   ├── royalty_rules / distributions   ← Роялти авторам
│   └── referral_rewards                ← Реферальные бонусы
│
├── schema: connections                 [Подключения к внешним системам]
│   ├── oauth_tokens                    ← pgcrypto encrypted
│   ├── api_keys                        ← pgcrypto encrypted
│   └── external_accounts               ← Discourse, LMS
│
└── schema: operations                  [Служебное: FSM, кэш, мониторинг]
    ├── fsm_states, content_cache       ← Эфемерное (можно дропнуть)
    ├── marathon_content, feed_*        ← Контент доставки
    └── error_logs, request_traces      ← Мониторинг
```

### 13.1 Критерии разделения на schemas

| # | Критерий | Вопрос | Если «нет» |
|---|----------|--------|-----------|
| 1 | **Один домен** | Все таблицы отвечают на один вопрос? | Разнести по разным schemas |
| 2 | **Независимый жизненный цикл** | Данные меняются по своему ритму? | Могут жить вместе |
| 3 | **Независимый доступ** | Разные сервисы/роли обращаются? | Могут жить вместе |
| 4 | **Тест канала** | Если убрать один канал (бот, web), домен останется? | Это домен, а не канал |

### 13.2 Bounded contexts

| Schema | Домен | Вопрос | Можно дропнуть? |
|--------|-------|--------|----------------|
| `public` | Идентичность | «Кто этот пользователь?» | Нет |
| `development` | Развитие | «Что делал, что знает и куда движется?» | Нет |
| `finance` | Финансы | «Сколько заплатил, заработал, на балансе?» | Нет |
| `connections` | Подключения | «Как подключены GitHub, LMS, WakaTime?» | Нет (секреты) |
| `operations` | Служебное | «FSM, кэш, логи?» | Да |

### 13.3 Отвергнутые варианты

| Кандидат | Решение | Причина |
|----------|---------|---------|
| `schema: bot` | Не нужна | Бот — канал, не домен. Не проходит тест канала |
| `schema: gamification` | Не нужна | Токены — finance, стадии — development.qualifications |
| `schema: learning` | Не нужна | Обучение — процесс, результаты = development |
| `schema: digital_twin` | Не нужна | ЦД = ВСЯ база, не одна schema |

### 13.4 Принцип именования

Все имена — **существительные-домены**: что содержит schema (development, finance, connections, operations). Не: кто использует (bot), не: процесс (billing), не: прилагательное (operational).

### 13.5 Потоки данных

```
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│   Бот    │  │   LMS    │  │   Клуб   │  │ Web App  │
└────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │              │
     ▼             ▼             ▼              ▼
┌──────────────────────────────────────────────────────┐
│  development.user_events (append-only)                │
│  = Activity Hub (единая точка записи)                 │
│  Бот: log_event() напрямую                            │
│  LMS/Клуб: HTTP webhook → API → INSERT               │
│  Web App: прямой INSERT                               │
└──────────────────┬───────────────────────────────────┘
                   │ on_event() / cron rebuild
      ┌────────────┼────────────┐
      ▼            ▼            ▼
┌──────────┐ ┌──────────┐ ┌──────────┐
│  skill   │ │ engage-  │ │ qualifi- │       ┌──────────────┐
│ mastery  │ │  ment    │ │ cations  │       │   finance.*  │
└──────────┘ └──────────┘ └──────────┘       │  (отдельный  │
      │            │            │            │   BC)        │
      └────────────┼────────────┘            └──────┬───────┘
                   ▼                                │
         ┌─────────────────┐                        │
         │  Views (на лету) │    tokens_earned ──────┘
         │  Рекомендации    │    payment_completed
         │  Open Learner    │
         │  Model           │
         └────────┬────────┘
                  ▼
         Event: recommendation_shown
         (цикл замыкается)
```

**Activity Hub — не отдельный сервис.** `log_event()` + HTTP endpoint. Каждый новый источник = новый producer в ту же таблицу.

**Связь Events → Finance:** события `tokens_earned`, `payment_completed` пишутся и в `development.user_events` (ЦД-аналитика), и в `finance.*` (финучёт). Два bounded context смотрят на одно действие с разных сторон.

---

## 14. Безопасность

### 14.1 Row-Level Security (RLS)

RLS на всех таблицах с `user_id`. Политика: пользователь видит только свои данные.

```
user_isolation: user_id = current_setting('app.current_user_id')
```

### 14.2 Роли PostgreSQL (5 ролей)

| Роль | Доступ | Ограничения |
|------|--------|-------------|
| `bot_service` | development (INSERT events, SELECT/UPDATE проекции), finance (SELECT/INSERT/UPDATE) | Полный доступ к Events + проекциям + finance |
| `mcp_user` | development (INSERT events декларативные, SELECT проекции) | RLS ограничивает до user_id текущего пользователя |
| `web_app` | development (INSERT events, SELECT проекции), finance (SELECT/INSERT) | Чтение + запись Events + finance |
| `analyst` | development (SELECT engagement, qualifications), finance (SELECT balances) | Только агрегаты, без PII (нет доступа к user_events, payments) |
| `financier` | finance (SELECT all) | Finance полный, остальное — нет |

### 14.3 Шифрование секретов

`connections.*` — pgcrypto (`pgp_sym_encrypt`). OAuth-токены и API-ключи зашифрованы at rest. Минимальный доступ — только сервис аутентификации.

---

## 15. Масштабирование и retention

| Метрика | Текущее | При 1K users | При 10K users |
|---------|---------|-------------|---------------|
| Events/месяц | ~1K | ~100K | ~1M |
| Размер user_events | <1MB | ~50MB | ~500MB |
| Размер проекций | <1MB | ~5MB | ~50MB |
| Neon tier | Free (512MB) | Free/Launch | Launch ($19/мес) |

**Retention policy:**
- Events < 2 лет → горячее хранилище (активные партиции)
- Events > 2 лет → архив (старые партиции detach → cold storage)
- Проекции → всегда актуальные (пересчёт из Events)

**Оптимизация rebuild (Фаза 3+):** При >10K событий на пользователя — snapshot-таблица `projection_snapshots` (JSONB snapshot + last_event_id). Rebuild = загрузить snapshot + replay после last_event_id. Триггер: rebuild > 1 сек.

**Параметры методов в БД (Фаза 3+):** При >30 карточек методов или per-skill BKT параметрах — таблица `indicator_registry` (indicator_id, computation_method, parameters JSONB, confidence_type, version). Триггер: потребность в per-skill настройке.

---

## 16. Эволюция проекций → CHR-Space

Начальные 5 проекций покрывают базовые потребности. Модель характеристик созидателя описывает **30+ измерений** (CHR-Space). Подобно тому как характеристики автомобиля (скорость, экономичность, безопасность) описывают его профиль, характеристики человека (агентность, обучаемость, стрессоустойчивость, калибр) описывают профиль созидателя.

### 16.1 Примеры будущих проекций

| Проекция | Из каких событий | Метод |
|----------|-----------------|-------|
| `agency` (агентность) | goal_set, self_assessed, task_completed | Composite score |
| `stress_resilience` (стрессоустойчивость) | engagement при нагрузке, recovery patterns | Time-series analysis |
| `techno_integration` (техноинтеграция) | WakaTime, экзокортекс usage, tool adoption | Aggregate + decay |
| `social_impact` (социальное влияние) | comment_created, question_answered, referral | Network analysis |
| `creativity` (креативность) | guide_published, methodology_contributed | Output + novelty score |
| `learning_velocity` (скорость обучения) | test_answered (accuracy over time per skill) | Regression slope |

Каждая новая проекция = новая таблица + replay событий. Архитектура Event Sourcing это позволяет: лог неизменяем, проекции создаются ретроспективно.

### 16.2 Исследовательская лаборатория (Фаза 4+)

«Лаборатория характеристик» — среда для работы с накопленными событиями:

1. **Экспериментировать** с новыми проекциями (replay на копии данных)
2. **Выявлять корреляции** между характеристиками (матрица корреляций)
3. **Обнаруживать синергии** (агентность + интеллект + калибр → лидерский потенциал)
4. **Строить персональную траекторию** (gap-анализ: Target(role) − Current(state))
5. **Валидировать методы** вычисления характеристик (A/B-тестирование формул)

**Триггер:** >1K пользователей с >6 мес историей событий.

---

*Статус: approved. Согласовано с архитектором.*
*Создано: 2026-02-24. Обновлено: 2026-03-11.*
