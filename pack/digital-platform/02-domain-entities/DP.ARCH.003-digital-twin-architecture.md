---
id: DP.ARCH.003
name: Архитектура цифрового двойника (3-слойная)
type: domain-entity
status: approved
summary: "3-слойная архитектура ЦД: Events → State → Views. Event Sourcing + CQRS. Доменные принципы проекций (BKT, HLR, engagement, misconceptions, qualifications). Эволюция: 5 начальных проекций → CHR-Space (30+ измерений). Реализация (Neon schemas, RLS, масштабирование) → C2.IT-Platform."
created: 2026-02-24
updated: 2026-04-04
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

> **Implementation Note.** Event Sourcing паттерн (Events → State → Views), типы событий, проекции (BKT, HLR, engagement) — домен. Конкретные схемы Neon (5 schemas), RLS, pgbouncer, Python snapshot code — текущая реализация. Детали: [C2.IT-Platform / Data-Stores](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/Data-Stores/).

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

#### Операционные (confidence = 1.0, WP-151 Ф3)

| event_type | source | payload (ключевые поля) | Что даёт |
|------------|--------|------------------------|----------|
| `onboarding_completed` | bot | `lang, path, duration, schedule_time, start_date` | Воронка: конверсия регистрации |
| `onboarding_step` | bot | `step` (language/name/duration/schedule/date/confirm) | Микро-воронка: где отваливаются |
| `mode_changed` | bot | `from_mode, to_mode, trigger` | Когда и почему меняют режим |
| `settings_changed` | bot | `field, old_value, new_value` | Паттерн изменения настроек |
| `reminder_delivered` | bot | `reminder_type, marathon_day` | Факт отправки напоминания |
| `reminder_opened` | bot | `source` | Конверсия: напоминание → действие |
| `error_shown` | bot | `error_key, handler` | UX-проблемы |
| `help_viewed` | bot | `source` (command/callback) | Индикатор «потерянности» |
| `progress_viewed` | bot | `view_type` (short/full) | Интерес к прогрессу |
| `marathon_completed` | bot | `total_topics, completed_topics, path` | Ключевая milestone-метрика |

### 4.3 Confidence как атрибут, не слой

Бывшее деление «declarative vs collected» заменяется **атрибутом confidence**:

| Тип сигнала | confidence | Пример |
|-------------|-----------|--------|
| Прямой тест / ДЗ | 0.9–1.0 | Правильный/неправильный ответ |
| Поведенческий | 0.7–0.9 | Session duration, streak |
| LLM-извлечённый | 0.4–0.7 | Misconception из чата |
| Самооценка | 0.3–0.5 | «Я знаю ZP.1 на 4 из 5» |

При Bayesian update: `confidence` используется как вес — тестовый ответ двигает P(known) сильнее, чем косвенный сигнал из чата.

### 4.4 Событие = факт, независимый от канала (Channel-Agnostic Events)

**Принцип:** Событие описывает **что произошло с пользователем**, а не где это произошло. `session_start` из бота и `session_start` из VS Code — одно и то же событие с разным `source`. Канал доставки — атрибут, не тип.

**Следствия:**

1. **Единый event store** для всех каналов. Бот, экзокортекс (Claude Code), LMS, Web App — все пишут в `development.user_events` через один API.
2. **source как атрибут.** Поле `source` (`bot`, `exocortex`, `lms`, `web_app`) — единственное отличие между каналами. Проекции (State) агрегируют по `user_id`, не по `source`.
3. **Канало-независимые типы** — события, которые возникают в нескольких каналах:

| event_type | Каналы | Примечание |
|---|---|---|
| `session_start` | bot, exocortex | Сессия работы |
| `ai_chat` | bot, exocortex | Вопрос ИИ-консультанту |
| `settings_changed` | bot, exocortex | Изменение профиля/настроек ЦД |
| `progress_viewed` | bot, exocortex | Просмотр ЦД (/twin, /me) |
| `note_created` | bot, exocortex | Заметка (fleeting note) |
| `knowledge_extracted` | bot, exocortex | Capture знания в Pack |
| `goal_set` | bot, exocortex | Установка цели |
| `self_assessed` | bot, exocortex | Самооценка |

4. **Канало-специфичные типы** — привязаны к конкретному каналу:

| event_type | Канал | Почему специфичен |
|---|---|---|
| `onboarding_*` | bot | TG-интерфейс регистрации |
| `reminder_*` | bot | Push-уведомления TG |
| `marathon_*`, `feed_*` | bot | Учебные модули бота |
| `error_shown` | bot | TG UI ошибок |
| `commit_pushed` | exocortex | Git workflow |
| `wp_completed` | exocortex | Закрытие РП |

**Антипаттерн:** писать в State (snapshot) минуя Events. Экзокортекс, пишущий напрямую в `digital_twins.data` вместо event store — нарушает Event Sourcing: нет лога, нет replay, нет ретроспективных запросов. Временно допустимо (dt-collect.sh для 2_6/2_7), но при появлении Activity Hub — мигрировать на events.

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
- Неправильный ответ → маппинг на `common_errors` из ячеек curriculum (MIM.MAP.001)
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
| Диагност (MIM.R.009) | skill_mastery + engagement | `priority(P) = gap(P) × impact(P)` — рекомендует фокус |

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

### 7.4 Nudge Engine (подсистема уведомлений)

**Вопрос:** «Когда и чем подтолкнуть пользователя к следующему действию?»

Nudge Engine — **View** в терминах 3-слойной архитектуры. Читает все 5 проекций State, генерирует персонализированные сообщения (nudges) и доставляет через доступные каналы.

```
          skill_mastery ──┐
          memory_decay  ──┤
          engagement    ──┼──→ Nudge Engine ──→ [TG Bot, Web App, Email]
          misconceptions──┤       (View)
          qualifications──┘
```

**Архитектурный принцип:** Nudge Engine — подсистема платформы (L2), а не функция бота. Бот — один из каналов доставки. Web App, email, push — равноправные каналы.

| Проекция | Что даёт для nudges |
|----------|-------------------|
| `skill_mastery` | Bottleneck-навыки, gap для рекомендаций |
| `memory_decay` | P(recall) < порог → напоминание о повторении |
| `engagement` | Streak, regularity, preferred_hour → таймер и триггеры |
| `misconceptions` | Фокусные области для learning nudges |
| `qualifications` | Стадия, тир → тип и тон сообщения |

**Связь с Intervention Loop (DP.M.007):** nudge = micro-intervention. Цикл: nudge → действие пользователя → событие → обновление State → новый nudge (или отсутствие).

Классификация типов nudges, стратегия эволюции и фазы развития → §17.

### 7.5 Замыкание цикла: Events → State → ИИ → новые Events

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

> **Implementation Note.** Конкретные системы (Railway, Neon, CF Workers, asyncpg), фазы внедрения с бюджетами задач → [C2.IT-Platform / Data-Stores / DP.ARCH.003-digital-twin-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/Data-Stores/DP.ARCH.003-digital-twin-implementation.md).

**Доменный принцип:** Внедрение идёт фазами: (0) Event Store → (1) Skill Mastery (BKT) → (2) Memory + Recommendations → (3) LLM Extraction → (4) LMS интеграция. Каждая фаза добавляет новую проекцию из одного event store.

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
- [MIM.FORM.006](../../../PACK-MIM/pack/mim/02-domain-entities/formalizations/MIM.FORM.006-thinking-profile-model.md) — Модель профиля мышления (вектор Bloom)
- [MIM.R.009](../../../PACK-MIM/pack/mim/02-domain-entities/MIM.R.009-diagnostician.md) — Диагност узких мест (bottleneck formula)
- [MIM.MAP.001](../../../PACK-MIM/pack/mim/07-map/MIM.MAP.001-competency-depth-cell-template.md) — Шаблон ячеек (skill_ids)

---

## 13. Хранилище: схемы данных

> **Implementation Note.** Конкретная реализация в Neon PostgreSQL: 5 schemas (public, development, finance, connections, operations), таблицы, потоки данных, именование → [C2.IT-Platform / Data-Stores / DP.ARCH.003-digital-twin-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/Data-Stores/DP.ARCH.003-digital-twin-implementation.md).

**Доменные принципы:**
- **ЦД = ВСЯ база данных целиком**, не отдельная schema
- 5 доменов: идентичность, развитие, финансы, подключения, служебное
- Критерии разделения: один домен, независимый жизненный цикл, независимый доступ, тест канала
- Activity Hub — единая точка записи событий из всех источников

---

## 14. Безопасность

> **Implementation Note.** RLS-политики, 5 ролей PostgreSQL (bot_service, mcp_user, web_app, analyst, financier), pgcrypto шифрование → [C2.IT-Platform / Data-Stores / DP.ARCH.003-digital-twin-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/Data-Stores/DP.ARCH.003-digital-twin-implementation.md).

**Доменные принципы:**
- RLS на всех таблицах с `user_id` — пользователь видит только свои данные
- Ролевая модель: сервисы, пользователи, аналитики, финансисты — разный доступ
- Секреты зашифрованы at rest

---

## 15. Масштабирование и retention

> **Implementation Note.** Метрики масштабирования, Neon pricing tiers, retention policy, snapshot оптимизация → [C2.IT-Platform / Data-Stores / DP.ARCH.003-digital-twin-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/Data-Stores/DP.ARCH.003-digital-twin-implementation.md).

**Доменные принципы:**
- Events < 2 лет → горячее хранилище, > 2 лет → архив
- Проекции всегда актуальные (пересчёт из Events)
- Snapshotting при rebuild > 1 сек

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

## 17. Nudge Engine: классификация сообщений и стратегия эволюции

> **Связь:** §7.4 (архитектурное место), DP.M.007 (Intervention Loop), DP.AISYS.014 (бот как канал доставки), WP-73 подсистема #21 (Notification Service).

### 17.1 Шесть типов nudge-сообщений

Каждый тип отвечает на свой вопрос и работает на определённом этапе жизненного цикла пользователя. Типы 1-5 покрывают T0-T3 (потребление), тип 6 — переход между тирами, тип 7 — T4 (созидание).

#### Тип 1: Адаптация (T0–T1)

**Функция:** провести нового пользователя через первые шаги на платформе.
**Почему важно:** без адаптации 70-80% пользователей уходят в первую неделю. Каждый шаг снижает барьер и открывает следующие возможности.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| А1 | «Заполни профиль — бот станет полезнее» | регистрация + 1д, профиль пустой | `profile_completeness` | Без профиля нет персонализации |
| А2 | «Попробуй марафон — 5 мин в день» | регистрация + 3д, marathon_status=null | `tier`, `marathon_status` | Марафон = основной путь в T2 |
| А3 | «Свяжи Aisystant — откроется лента» | T1, aisystant не привязан, active_days≥3 | `aisystant_linked`, `active_days` | Привязка Aisystant = путь в T2+ |
| А4 | «Первый шаг сделан! Вот что дальше» | первое событие любого типа | `events_total` | Позитивное подкрепление на старте |

#### Тип 2: Обучение (T2+)

**Функция:** поддержать ритм обучения и помочь не застрять.
**Почему важно:** обучение требует регулярности. Без внешних напоминаний пользователи теряют ритм, а незавершённые марафоны и непроверенные знания обесценивают вложенное время.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| О1 | «У вас {N} незавершённых уроков — продолжить?» | marathon_status=active, steps_remaining>0, неактивен 2д | `marathon_progress`, `last_active_date` | Незавершённый марафон = потерянный прогресс |
| О2 | «Новый дайджест в ленте — тема: {topic}» | новый дайджест + feed_status=active | `feed_status`, `interests` | Лента = ежедневная привычка |
| О3 | «Пора повторить {skill} — знание угасает» | memory_decay: P(recall) < 0.5 | `memory_decay`, `skill_mastery` | HLR = научный spaced repetition |
| О4 | «Assessment по {course} доступен — проверь себя» | course_progress ≥ 80%, assessment не пройден | `course_progress`, `assessments` | Assessment = объективная оценка |

#### Тип 3: Вовлечённость (T3+)

**Функция:** удержать активного пользователя, предотвратить отток.
**Почему важно:** стоимость удержания в 5-7 раз ниже привлечения. Ранний сигнал оттока (3 дня неактивности) — окно для вмешательства.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| В1 | «Привет! Вас не было {N} дней» | last_active_date + 3д | `last_active_date`, `streak` | Ранний сигнал оттока |
| В2 | «Серия прервана ({streak} дней). Один урок — и снова в деле» | streak_drop (было≥3, стало 0) | `longest_streak`, `active_days_streak` | Серия = мощный мотиватор |
| В3 | «Активность низкая. 5 минут с дайджестом?» | events_last_7d < 2 | `events_last_7d`, `regularity` | Порог оттока |
| В4 | «Марафон ждёт! /learn» | marathon_stalled 3д | `marathon_status`, `last_active_date` | Марафон = структурированный путь |

#### Тип 4: Признание (все тиры)

**Функция:** отметить достижения пользователя. Позитивное подкрепление.
**Почему важно:** признание формирует внутреннюю мотивацию. Без обратной связи о прогрессе пользователь не ощущает движения.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| П1 | «{N} сессий! {поздравление по контексту}» | sessions_total = 10/25/50/100 | `sessions_total` | Внутренняя мотивация |
| П2 | «{N} активных дней! {поздравление}» | active_days = 7/14/30 | `active_days` | Формирование привычки |
| П3 | «Марафон завершён: {course}!» | marathon_completed | `marathon_status`, `completed_marathons` | Кульминация опыта |
| П4 | «Первый assessment пройден!» | первый assessment_passed | `assessments` | Объективная верификация знаний |
| П5 | «Стадия повысилась: {old} → {new}!» | qualifications.stage изменилась | `qualifications.stage` | Самая значимая метрика развития |

#### Тип 5: Возвращение (все тиры, отток)

**Функция:** вернуть ушедших пользователей.
**Почему важно:** пользователи с историей ценнее новых — у них уже есть прогресс, контекст и данные в ЦД. Возвращение дешевле привлечения.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| Вз1 | «Давно не виделись. Есть {N} новых дайджестов» | неактивен 14д + новый контент | `last_active_date`, `unread_digests` | Контентная приманка |
| Вз2 | «Ваш прогресс сохранён. {skill} ещё помните?» | неактивен 30д | `skill_mastery`, `memory_decay` | Привязка к достижениям |
| Вз3 | «{N} учеников прошли марафон на этой неделе» | неактивен 7д, есть данные по когорте | `peer_stats` (агрегат) | Социальное доказательство |

#### Тип 6: Траектория (T1–T3)

**Функция:** подсказать следующий шаг в развитии (tier progression, skill gap).
**Почему важно:** без видимой траектории пользователь не понимает куда двигаться. Прозрачность пути повышает мотивацию и удержание.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| Тр1 | «Для T2 осталось: {чеклист}» | tier=T1, >50% условий выполнено | `tier`, `tier_progress` | Прозрачность пути |
| Тр2 | «Попробуй ленту — следующий уровень» | tier=T1, marathon_completed≥1 | `completed_marathons`, `feed_status` | Переход T1→T2 |
| Тр3 | «ЦД готов — подключи /twin» | tier=T2, достаточно данных для ЦД | `events_total`, `skill_count` | Переход T2→T3 |
| Тр4 | «Рекомендация: фокус на {skill} — узкое место» | tier≥T3, анализ ЦД | `skill_mastery`, `qualifications` | Персонализированный вектор развития |

#### Тип 7: Созидание (T4)

**Функция:** поддержать пользователя в развитии собственной IWE и вкладе в сообщество.
**Почему важно:** T4 — качественно другой этап. Пользователь уже не только учится, а создаёт: строит свою интеллектуальную рабочую среду, публикует знания, помогает другим. Без поддержки этот переход от потребителя к созидателю часто не происходит.

| # | Сообщение | Триггер | Данные из ЦД | Зачем |
|---|-----------|---------|-------------|-------|
| С1 | «Твой экзокортекс: {подсказка по настройке IWE}» | tier=T4, экзокортекс активен | `exocortex_usage`, `tools_adopted` | Поддержка в развитии собственной IWE |
| С2 | «Новый инструмент для твоей IWE: {tool}» | tier=T4, новый MCP/инструмент в каталоге | `tools_adopted`, `exocortex_config` | Расширение инструментария |
| С3 | «Попробуй опубликовать: у тебя знания по {topic}» | tier=T4, skill_mastery>{порог} по кластеру | `skill_mastery`, `publications` | Вклад в сообщество |
| С4 | «{N} учеников изучают тему, где ты силён — помоги как наставник» | tier=T4, skill_mastery>0.8, есть ученики с gap | `skill_mastery`, `peer_gaps` | Наставничество |
| С5 | «Твой вклад за месяц: {публикации, ревью, ответы}» | tier=T4, конец месяца | `publications`, `reviews`, `answers` | Признание созидательной роли |

### 17.2 Матрица «тип × тир»

| Тип | T0 | T1 | T2 | T3 | T4 |
|-----|----|----|----|----|-----|
| **Адаптация** | ✅ | ✅ | — | — | — |
| **Обучение** | — | — | ✅ | ✅ | ✅ |
| **Вовлечённость** | — | — | — | ✅ | ✅ |
| **Признание** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Возвращение** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Траектория** | — | ✅ | ✅ | ✅ | — |
| **Созидание** | — | — | — | — | ✅ |

### 17.3 Стратегия эволюции: от правил к агентной системе

Nudge Engine развивается в 4 фазах. Каждая фаза добавляет уровень «интеллекта» — от статических правил к автономному агенту.

```
Фаза 1: ПРАВИЛА      Фаза 2: КОНТЕКСТ       Фаза 3: АДАПТАЦИЯ     Фаза 4: АГЕНТ
(MVP, done)           (ЦД-персонализация)    (самонастройка)        (автономный)
─────────────────────────────────────────────────────────────────────────────────────
Статичные правила     Правила + контекст ЦД  Правила + обр. связь   LLM-агент
Статичный текст       AI-генерация текста    A/B-тестирование       Самостоятельный текст
Только T3+            Все тиры               Все тиры + сегменты    Индивидуальный подход
Только бот            Бот + email            Мультиканал            Оптимальный канал
7-30д cooldown        Динамический cooldown  Адаптивный таймер      Самопланирование
Нет обр. связи       Трекинг доставки       CTR + метрики отклика  Целеполагание (goal-seeking)
```

#### Фаза 1: Правила (текущее MVP, done)

- 6 правил типа «Вовлечённость» + «Признание», статичные тексты (i18n)
- Только T3+ (ЦД подключён)
- Канал: Telegram-бот, ежедневно 13:00 MSK
- **Данные:** engagement projection + user_meta
- **Реализация:** `core/engagement_analyzer.py` (декоратор `@rule`)

#### Фаза 2: Контекст (следующий шаг)

- Все 7 типов сообщений, все тиры (T0–T4)
- AI-генерация текста через Haiku с контекстом из ЦД (не шаблон, а персонализированное сообщение)
- Динамический cooldown на основе `preferred_hour`, `regularity`
- Доставка в оптимальное время (`preferred_hour` из engagement projection)
- **Данные:** все 5 проекций ЦД

#### Фаза 3: Адаптация (самонастройка)

- Feedback loop: отправлено → доставлено → прочитано → действие → влияние на метрики
- A/B-тестирование текстов и таймингов
- Сегментация: по тиру, стадии, паттерну поведения
- Динамические пороги: адаптируются к когорте (не фиксированные)
- **Данные:** nudge_log расширяется: `{sent, delivered, read, acted, impact_delta}`

#### Фаза 4: Агент (автономный)

- LLM-агент с доступом к ЦД через MCP
- Сам формулирует nudge, выбирает канал, время, частоту
- Goal-seeking: оптимизирует на engagement + learning outcomes
- Саморефлексия: анализирует собственную эффективность
- Self-knowledge: объясняет пользователю как работает nudge-система
- **Данные:** полный ЦД + nudge history + outcome metrics

### 17.4 Принципы Nudge Engine

1. **Платформенность:** Nudge Engine — подсистема платформы (L2), не функция бота. Бот, Web App, email — равноправные каналы доставки.
2. **ЦД-центричность:** Каждый nudge опирается на данные ЦД. Без данных — нет nudge (кроме Адаптации T0).
3. **Micro-intervention:** nudge = минимальное вмешательство. Один nudge — одно действие. Не перегружать.
4. **Opt-out:** Пользователь всегда может отключить nudges (целиком или по типам).
5. **Cooldown:** Не более одного nudge в день на пользователя. Минимальный cooldown между однотипными nudges.
6. **Экзоскелет, не протез:** nudge усиливает мышление пользователя, не заменяет. Подсказывает «что дальше», а не решает за него.
7. **Измеримость:** Каждый nudge должен быть отслеживаемым (отправлен → доставлен → действие).
8. **Эволюционируемость:** Новый тип nudge = новое правило + новая проекция. Архитектура Event Sourcing это позволяет.

---

## 18. Маппинг: ARCH.003 ↔ DT MCP метамодель (WP-151 Ф6)

> **Контекст:** Два представления одних данных. ARCH.003 описывает **архитектурные слои** (Events → State → Views). Метамодель DT MCP (`DS-MCP/digital-twin-mcp/metamodel/`) описывает **типы индикаторов** (IND.1-4). Этот маппинг связывает их.

### 18.1 Слои ↔ типы индикаторов

| ARCH.003 слой | IND-тип метамодели | Кол-во | Confidence | Примеры IND-кодов |
|---|---|---|---|---|
| **Events** | IND.1 (declarative) | 20 | 0.3-0.5 | IND.1.1 (профиль), IND.1.2 (цели), IND.1.5 (стиль подачи) |
| **Events** | IND.2 (collected) | 30+ | 0.7-1.0 | IND.2.1 (сессии), IND.2.6 (кодирование), IND.2.10 (история) |
| **State** | IND.3 (derived) | 37 | вычисляемый | IND.3.1 (агентность), IND.3.2 (мастерство), IND.3.4 (квалификация) |
| **Views** | IND.4 (generated) | 3 | on-demand | IND.4.3 (прогнозы), IND.4.4 (отчёты) |

**Принцип соответствия:** IND.1 и IND.2 = Events с разным `confidence` (§4.3). IND.3 = State (проекции). IND.4 = Views (генерируемые, не хранятся). Четвёрка IND.1-4 -- историческое деление по источнику; архитектурно значим только слой (Events/State/Views).

### 18.2 Проекции ARCH.003 → IND.3 группы метамодели

| Проекция (§5) | Метод | IND.3 группа | Конкретные индикаторы | Реализация |
|---|---|---|---|---|
| **skill_mastery** | BKT | IND.3.2 (mastery) | IND.3.2.01 (практика), IND.3.2.04 (мировоззрение) | BKT v0.8 (`dt_calc.py`): P(mastery) по 72 мемам (PD.CAT.001) |
| **memory_decay** | HLR | -- | -- | Не реализована. Требует >100K событий |
| **engagement** | Агрегаты | IND.3.1 (agency), IND.3.10 (integral) | IND.3.1.02 (slot_regularity), IND.3.10.1 (agency_index) | `engagement` VIEW + `dt_sync` + `dt_calc.py` v0.7 |
| **misconceptions** | LLM + pattern | IND.3.3.04 (мемы), IND.3.11.02 (заблуждения) | IND.3.3.04 (текущие мемы по BKT) | BKT по мемам (Ф5). LLM extraction -- не реализована |
| **qualifications** | Threshold rules | IND.3.4, IND.3.11.01 | IND.3.4.01 (ступень ученика), IND.3.11.01 (диагн. состояние) | `student_stage` в `dt_calc.py` (learner + builder path) |

### 18.3 Вторая ось: 5 областей развития (PD.FORM.080)

Помимо проекций (горизонтальный срез по методу), метамодель содержит группировку по **областям развития** -- вертикальный срез по предмету:

| Область (FORM.080) | IND.3 индикаторы (мировоззрение) | IND.3 индикаторы (мастерство) |
|---|---|---|
| knowledge | IND.3.2.04, IND.3.3.04 | IND.3.2.01, IND.3.2.05 |
| tools | IND.3.9.1 | IND.3.2.02, IND.3.2.03, IND.3.9.2 |
| constraints | IND.3.3.03 | IND.3.1.04, IND.3.6.04 |
| environment | IND.3.3.05, IND.3.3.02 | IND.3.7.1, IND.3.7.2 |
| organism | IND.3.6.01 | IND.3.6.02, IND.3.6.03, IND.3.6.05 |

Реализация: `mapping.js` (DT MCP, `AREA_MAPPING`). Source-of-truth: PD.FORM.080 §9, PD.FORM.081.

### 18.4 Известные разрывы

| # | Разрыв | Влияние | Где закрывается |
|---|---|---|---|
| 1 | memory_decay (HLR) не реализована | Нет spaced repetition, нет `next_review_date` | WP-151 Ф8+ (при >100K событий) |
| 2 | misconceptions: LLM extraction не реализована | Только BKT по мемам, нет анализа чатов | WP-151 Ф7 (Лаборатория) |
| 3 | ~10 индикаторов IND.3 без вычисления | IND.3.3.05 (калибр), IND.3.5 (роли), IND.3.9 (AI usage) | WP-175 (маппинг метамодели) |
| 4 | Области Worldview и Exocortex: недостаточно индикаторов | 2 инд. для мировоззрения, 0 для экзокортекса | WP-175 (GAP-лист) |
| 5 | dt_calc.py пороги ступеней не совпадают с нормами метамодели | Proxy (events/sessions) вместо duration-of-stability | WP-175 (калибровка) |

### 18.5 Потоки данных (текущее состояние, апрель 2026)

```
Бот (log_event)  ──→ development.user_events ──→ engagement VIEW ──→ dt_sync ──→ digital_twins
     20 event_types          Neon                  15 метрик          04:30 MSK      JSONB
                                                                         │
                                                                         ▼
                                                                    calculate_derived()
                                                                    dt_calc.py v0.8
                                                                         │
                                                                         ▼
                                                              3_derived (3 проекции):
                                                              - slot_regularity (IND.3.1.02)
                                                              - student_stage (IND.3.4.01)
                                                              - integral_agency_index (IND.3.10.1)
                                                              - mastery_by_area (BKT, IND.3.2.04)
                                                              - worldview_gaps (BKT, IND.3.3.04)
```

> **Связь с WP-175:** WP-175 формализует полный маппинг таблица индикатор → область → норма в PD.FORM.080 §10. Этот раздел описывает архитектурное соответствие слоёв.

---

*Статус: approved. Согласовано с архитектором.*
*Создано: 2026-02-24. Обновлено: 2026-04-04 (§4.2 + §4.4 + §18: WP-151 Ф3/Ф6).*
