---
id: DP.ECON.001
name: Points Engine — движок начисления баллов
type: domain-entity
status: draft
summary: "Доменная модель системы баллов: сущности, инварианты, формула, потоки. Source-of-truth для Points Engine (WP-121). Реализация: база platform, схема points."
created: 2026-04-13
updated: 2026-04-13
related:
  realizes: [DP.SC.105]
  uses: [DP.ARCH.006, DP.SYS.001]
  source: "WP-121 Ф0 калибровка 9 апр 2026, WP-121 Ф1 миграции 13 апр 2026"
tags: [points, contribution-economy, gamification, billing]
---

# [DP.ECON.001] Points Engine — движок начисления баллов

> **Обещание потребителю:** DP.SC.105 — Экономика вклада.
> **Реализация:** база `platform`, схема `points` (миграция 010-points-schema.sql, WP-121 Ф1, 13 апр 2026).
> **Калибровка:** WP-121 Ф0, 9 апр 2026 — 66 934 events, 102 users, целевой payout 20.4%.

---

## 1. Ключевое различение

**Баллы = вычисляемая проекция**, а не хранимое состояние.

```
user_events (факты, append-only)
  │
  ▼
Readiness Gate (WP-109 Ф4)
  │ все источники synced + checksum OK?
  ▼
calculate_points()
  │ event + point_rules → effective_points
  ▼
points.point_transactions (начисления, append-only)
  │
  ▼
points.point_balances (агрегат-кэш)
  │
  ▼
Бот: /points (пользователь видит)
```

Пересчёт = replay всех events по текущим rules. Один event → максимум одна транзакция.

---

## 2. Формула начисления

```
effective_points = min(Base × ActionType × Streak × Qualification, daily_cap)
```

| Множитель | Источник | Диапазон |
|-----------|---------|---------|
| **Base** | `points.point_rules.points` | 2–69 pts/event |
| **ActionType** | `points.point_rules.category` | ×1 / ×2 / ×3 / ×5 |
| **Streak** | `points.point_balances.streak_multiplier` | ×1.0 – ×1.5 |
| **Qualification** | `points.qualification_multipliers.multiplier` | ×1.0 – ×5.0 |
| **daily_cap** | `points.qualification_multipliers.daily_cap` | 100 – 1000 |

---

## 3. Сущности

### 3.1. point_rules — правила начисления

Какой `event_type` → сколько базовых баллов, лимит в день.

| Поле | Тип | Описание |
|------|-----|---------|
| `id` | SERIAL PK | — |
| `event_type` | TEXT | Тип события (topic_created, wp_completed, …) |
| `source` | TEXT | lms / bot / iwe / club / NULL=любой |
| `category` | TEXT | time / wp / quality / platform / condition / none |
| `points` | INTEGER | Базовые баллы (0 для none/condition) |
| `max_per_day` | INTEGER | Лимит событий типа за день (anti-abuse) |
| `active` | BOOLEAN | Версионирование: false = деактивировано |
| `valid_from/until` | TIMESTAMPTZ | Для replay по историческим правилам |

**Категории действий (ActionType):**

| Категория | Множитель | Примеры |
|-----------|-----------|---------|
| `time` | ×1 | text_submitted, commit_created, ai_chat |
| `wp` | ×2 | wp_completed, content_published |
| `quality` | ×3 | knowledge_extracted, distinction_added |
| `platform` | ×5 | fmt_commit_merged |
| `condition` | — | day_open, day_close (условие, не баллы) |
| `none` | — | session_start, reminder_delivered |

> Примечание: base points в таблице уже отражают категорийный множитель (откалиброваны ×2.3 от единицы для достижения payout 20%). `ActionType` в формуле = 1 при category=time, т.е. реализован через разницу в `points`.

### 3.2. qualification_multipliers — 8 степеней квалификации МИМ

Источник: [system-school.ru/qualification](https://system-school.ru/qualification).

| Квалификация | Множитель | Daily cap | Описание |
|---|---|---|---|
| Ученик | ×1.0 | 100 | Ежедневный слот, 10+ ч/нед |
| Работник | ×1.3 | 140 | Рациональная работа, причинно-следственный анализ |
| Стратег | ×1.6 | 200 | Выбор метода в неопределённости |
| Специалист | ×2.0 | 280 | Инженерный процесс, широкая эрудиция |
| Практик | ×2.5 | 360 | Применяет на практике, организует до 10 человек |
| Мастер | ×3.0 | 500 | Проекты оргразвития в масштабе компании |
| Реформатор | ×4.0 | 700 | Масштаб отрасли и сообщества |
| Общественный деятель | ×5.0 | 1000 | Цивилизационный масштаб |

**Квалификация ≠ тир подписки (DP.D.047).** Тир = уровень доступа (оплата). Квалификация = степень мастерства (поведение + экзамен МИМ, 3 года).

Квалификация читается из ЦД (`digital_twins.data.3_derived`). Points Engine **не вычисляет** квалификацию — только читает.

### 3.3. point_transactions — журнал начислений (append-only)

| Поле | Тип | Описание |
|------|-----|---------|
| `id` | BIGSERIAL PK | — |
| `user_id` | TEXT | Ory UUID (T1+) или telegram_id::text (T0) |
| `event_id` | BIGINT UNIQUE | → development.user_events.id (1:1, NULL для spent/manual) |
| `rule_id` | INTEGER | → point_rules.id |
| `points` | INTEGER | Effective points (уже с множителями, может быть отрицательным) |
| `type` | TEXT | earned / spent / correction / expired / manual |
| `qualification` | TEXT | Снимок квалификации на момент начисления |
| `source_region` | TEXT | world / ru (для аудита по юрисдикциям) |
| `reference_id` | BIGINT | Для correction → оригинальная транзакция |
| `action_type_mult` | NUMERIC | Множитель категории (аудит + SBT) |
| `streak_mult` | NUMERIC | Множитель streak (аудит) |
| `qual_mult` | NUMERIC | Множитель квалификации (аудит) |
| `action_hash` | TEXT | SHA-256(event_id, rule_id, user_id, points) — для SBT |
| `verified_by` | TEXT | Верификатор (будущее: peer-review) |
| `milestone_eligible` | BOOLEAN | TRUE = засчитывается в milestone для SBT |

**Типы транзакций:**
- `earned` — начисление по правилу (calculate_points)
- `spent` — списание при оплате баллами (Billing Service, WP-183)
- `correction` — ручная корректировка (±, reference → оригинал)
- `expired` — истечение срока (политика expiry, будущее)
- `manual` — ручное начисление администратором

### 3.4. point_balances — агрегированный баланс (кэш)

| Поле | Описание |
|------|---------|
| `user_id` | TEXT PK — Ory UUID или telegram_id::text |
| `total_earned` | Сумма всех earned транзакций |
| `total_spent` | Сумма всех spent транзакций |
| `total_corrections` | Сумма всех correction транзакций |
| `balance` | total_earned − total_spent + total_corrections |
| `streak_days` | Текущий streak (дней подряд) |
| `streak_multiplier` | Текущий множитель ×1.0–×1.5 |
| `last_active_date` | Дата последней активности (для расчёта streak) |

**Это кэш, не source-of-truth.** Source-of-truth = `point_transactions`. При replay: TRUNCATE point_balances → пересчёт из транзакций.

---

## 4. Инварианты

| # | Инвариант | Механизм |
|---|-----------|----------|
| I1 | Один event → максимум одно начисление | UNIQUE(event_id) в point_transactions |
| I2 | Events неизменяемы | Append-only user_events, нет UPDATE/DELETE |
| I3 | Транзакции неизменяемы | Append-only. Ошибка → INSERT correction |
| I4 | Баланс = сумма транзакций | point_balances = materialized SUM (кэш) |
| I5 | Начисление только при Readiness Gate OK | calculate_points() проверяет sync_log |
| I6 | Idempotent replay | Тот же event + тот же rule = тот же результат |

---

## 5. Streak

| День | Множитель |
|------|-----------|
| 1 | ×1.0 |
| 2 | ×1.07 |
| 3 | ×1.14 |
| … | … |
| 7+ | ×1.5 (максимум, удерживается) |
| Пропуск | сброс → ×1.0 |

«День» = Day Open + хотя бы 1 event из любого источника.

Штраф за отсутствие Day Open (×0.5) **отложен** до >30% активных дней содержат Day Open (сейчас 0.7%). Включать через `active=false` в соответствующем правиле.

---

## 6. Конвертация: баллы → скидки

- 1 балл = $0.01 (или эквивалент в рублях по курсу)
- Применяется к: практикумам, семинарам, резидентурам, менторским сессиям, приоритетной ИИ-поддержке
- Скидка оформляется в юрлице покупки (РФ или мир, WP-215)
- Скидка может покрывать 100% стоимости (допустимо: вклад заслуживает бесплатного доступа)

---

## 7. Потоки данных

### 7.1. Начисление (earned)

```
user_events.INSERT (Activity Hub, WP-109)
  → Readiness Gate: все источники synced + checksum?
  → calculate_points(event, rules)
  → INSERT point_transactions (type='earned')
  → UPSERT point_balances
```

### 7.2. Списание (spent)

```
Участник → /buy_with_points → Billing Service (WP-183)
  → SELECT balance FROM point_balances
  → balance >= price?
  → INSERT point_transactions (type='spent', points=-N)
  → UPDATE point_balances
  → Выдать доступ
```

### 7.3. Replay (пересчёт)

```
Триггер: новые правила / исправление / аудит
  → TRUNCATE point_transactions (или archive)
  → FOR EACH event IN user_events ORDER BY created_at:
      calculate_points(event, rules_at(event.created_at))
  → Пересчёт point_balances
```

---

## 8. Readiness Gate (WP-109 Ф4)

Перед каждым вызовом `calculate_points()`:
1. Каждый source: последний sync = success, не старше 48h
2. Checksum: count в API источника == count в Neon (расхождение ≤3%)
3. Все проверки OK → начисление. Хоть одна ❌ → СТОП + алерт

Без Gate баллы начислялись бы по неполным данным → неточные балансы → потеря доверия.

---

## 9. Антизлоупотребления

| Механизм | Как работает |
|----------|-------------|
| `max_per_day` в point_rules | Лимит событий одного типа за день |
| `daily_cap` в qualification_multipliers | Потолок баллов за день по квалификации |
| Readiness Gate | Начисление только по подтверждённым данным |
| UNIQUE(event_id) | Дедупликация: один event = одна транзакция |
| Append-only | Нельзя задним числом изменить транзакцию |

---

## 10. Будущее: SBT (Soulbound Tokens)

**Триггер:** 500+ активных участников.

Поля подготовлены в `point_transactions`:
- `action_hash` — SHA-256 fingerprint транзакции
- `verified_by` — верификатор (peer-review, admin)
- `milestone_eligible` — засчитывается в milestone для mint

Стек: ERC-5192 (non-transferable), Base L2, Coinbase Smart Wallet.
Оценка: 60–110h, gas ~$5–50/мес.

---

## 11. Результаты калибровки (Ф0, 9 апр 2026)

> Данные: 66 934 events, 102 users (67 с >0 баллами), период 2020–2026.

| Сценарий | Payout% | Возврат/год |
|----------|---------|------------|
| A: без streak, без штрафа | 20.4% | $2 047 |
| B: + streak (рекомендуется) | 24.0% | $2 408 |
| C: + streak + штраф ×0.5 | 13.8% | $1 384 |

**Рекомендуемый сценарий B** (streak без штрафа). Включить штраф при >30% активных дней с Day Open.

---

## 12. Связи

| Сущность | Связь |
|----------|-------|
| DP.SC.105 | Реализует обещание «Экономика вклада» |
| DP.ARCH.006 | Память.Derived предоставляет квалификацию для множителя |
| DP.SYS.001 | Activity Hub поставляет user_events |
| WP-109 | Readiness Gate + Activity Hub |
| WP-183 | Billing Service вызывает spent-транзакции |
| WP-121 | РП реализации (Ф0–Ф5) |
| WP-215 | Региональное разделение (юрлицо конвертации) |

---

*Создано: 2026-04-13. WP: 121 Ф1 Pack. Калибровка: Ф0, 9 апр 2026.*
