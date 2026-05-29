---
id: DP.SC.147
name: Агрегирующий пайплайн cognitive brief
type: sc
status: draft
layer: L2-Platform
summary: "Навигатор (MIM.R.007) перед ответом читает агрегированный brief из выходов Оркестратора, Портного, activity_log и Cognitive Proxy. Без text_analysis consent — только детерминированные поля."
consumer: MIM.R.007
created: 2026-05-18
updated: 2026-05-18
related:
  realizes: []
  uses:
    - DP.SC.142   # Текстовый анализ — источник cognitive_profile
    - DP.ROLE.052 # Cognitive Proxy Analyst — исполнитель text_analysis
    - DP.SC.004   # Knowledge Entry — источник captures
    - DP.SC.020   # Event Ingest — источник событий
  extends: []
---

# DP.SC.147 — Агрегирующий пайплайн cognitive brief

## Правило (инвариант)

> **Scope Guard (per-field):** Каждое поле brief'а читается через свой домен/доступ. Нет единого DB role с полным доступом.

| Поле | Источник | Модель доступа |
|------|----------|---------------|
| `orchestrator_brief` | Оркестратор | Чтение выхода роли, read-only |
| `tailor_recommendation` | Портной | Чтение выхода роли, read-only |
| `stuck_analysis` | activity_log + DT | Чтение из DT + activity_log, read-only |
| `cognitive_profile` | cognitive schema | `cognitive_proxy_reader` (DP.ROLE.052), read-only |

- [ ] `get_cognitive_brief` — API-уровень (фасад). Агрегирует за фасадом, каждая часть через свой доступ.
- [ ] `cognitive_profile` (cp.wld/cp.agt/bh.awr) доступно ТОЛЬКО при наличии `text_analysis` consent. Без consent — поле = null, остальные три поля работают.
- [ ] Без `text_analysis` consent ведение НЕ пропадает: Навигатор получает `orchestrator_brief` + `tailor_recommendation` + `stuck_analysis`.
- [ ] Пайплайн НЕ пишет в DT, НЕ влияет на баллы/статус/аттестацию. Только read-only для рекомендаций.
- [ ] Parliament Model: `attestator` и `points_calculator` — no access к `cognitive` schema. Только `cognitive_proxy_reader`.
- [ ] PII Gate (B7.3): до закрытия — `cognitive_profile` не формируется из реальных текстов (fallback на детерминированные агрегаты или null).

## Обещание

**Кому:** Навигатор (MIM.R.007) — для персонализации ответа в диалоге.

**Зачем:** Навигатору нужен единый срез состояния пилота перед ответом, чтобы адаптировать тон, глубину и направление. Без этого Навигатор оперирует только детерминированными данными из DT и не видит «где пилот застрял сегодня».

**Что получит:**
1. `orchestrator_brief` — режим дня, trigger_kind (из Оркестратора).
2. `tailor_recommendation` — next_best_action, занятие дня (из Портного).
3. `stuck_analysis` — поведенческие сигналы: пропуски сессий, зависшие WP, незавершённые уроки (из activity_log + DT).
4. `cognitive_profile` — cp.wld, cp.agt, bh.awr (из cognitive schema, ТОЛЬКО с consent).

**Триггер:**
- Ежедневный cron (04:00 МСК) — полный пересчёт brief для всех пилотов.
- Сигнальщик инвалидации профиля (DP.ROLE.042 §3.4, `diagnostician-watcher`) — event-driven обновление при сигнале «застрял» (domain_event).
- On-demand — при первом запросе Навигатора за день, если cron ещё не отработал.

**Время отклика:**
- Cron: brief доступен к 04:05 МСК.
- Event-driven: ≤ 2 минут после domain_event.
- On-demand: ≤ 5 секунд (чтение из кэша / materialized view).

**Режим отказа:**
- Пайплайн недоступен → Навигатор работает в fallback mode (только `read_digital_twin` + activity_log).
- `cognitive_profile` недоступен (нет consent / PII Gate не закрыт) → поле = null, остальные три поля работают.
- Один из источников (Оркестратор / Портной) недоступен → brief формируется из оставшихся источников + лог `partial: true`.

## Свидетельства (критерий приёмки)

**Данные:**

| Критерий | Как проверить |
|----------|--------------|
| Таблица/материализованное представление `cognitive.brief` существует | `\dt cognitive.brief` в psql |
| 4 поля заполнены для пилота с consent | `SELECT ory_identity, orchestrator_brief, tailor_recommendation, stuck_analysis, cognitive_profile FROM cognitive.brief LIMIT 1` |
| Пилот без consent имеет `cognitive_profile = null`, остальные поля заполнены | `SELECT * FROM cognitive.brief WHERE cognitive_profile IS NULL AND orchestrator_brief IS NOT NULL` |
| `attestator` role не имеет доступа к `cognitive` schema | `\dp cognitive.*` → no grants for attestator_role |

**Контекст:**

| Условие | Проверка |
|---------|---------|
| text_analysis consent | `SELECT scope FROM learning.consent_grant WHERE ory_identity = ? AND scope = 'text_analysis' AND granted = true AND revoked_at IS NULL` |
| PII Gate закрыт | Документ `security/PII-gate-WP316.md` — все 5 пунктов ✅ |
| Cron отработал | Лог `/var/log/cognitive-brief-cron.log` или метрика `cognitive_brief_last_run` |

**Полномочия:**

| Роль | Что подтверждает |
|------|-----------------|
| DP.ROLE.052 Когнитивный прокси-аналитик | Что `cognitive_profile` вычислено по scope guard |
| DP.ROLE.041 Аттестатор | Что `stuck_analysis` не влияет на stage (изоляция) |
| VR.R.001 Аудитор | Что per-field доступ реализован корректно (нет единого full-access role) |

**Свидетельства:**

| Свидетельство | Источник |
|--------------|---------|
| Audit log per-field access | `cognitive.brief_access_log` |
| Отсутствие записей `cognitive_profile` для пилотов без consent | SQL-запрос → 0 rows с `cognitive_profile IS NOT NULL` для no-consent |
| Навигатор использует brief в 90%+ диалогов (T2+) | Метрика `navigator_brief_usage_rate` |

## Реализующие сервисы (MAP.002)

| Сервис | Роль | Триггер |
|--------|------|---------|
| Cognitive Brief Composer | DP.ROLE.052 + pipeline worker | ⏰ Ежедневный cron |
| Сигнальщик инвалидации профиля | DP.ROLE.042 §3.4 | 📩 domain_event (stuck signal) |
| `get_cognitive_brief` | MCP tool (Gateway) | 🔍 Навигатор запрос |

## Пользовательский путь

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Оркестратор вычисляет режим дня | Оркестратор | DP.SC.135 / Agent Inbox |
| 2 | Портной формирует занятие | Портной | DP.SC.107 |
| 3 | Cognitive Proxy анализирует тексты (при consent) | DP.ROLE.052 | DP.SC.142 |
| 4 | activity_log агрегирует stuck-сигналы | activity-hub | `development.user_events` |
| 5 | Brief Composer агрегирует 4 поля в `cognitive.brief` | Pipeline worker | DP.SC.147 |
| 6 | Навигатор читает brief перед ответом | MIM.R.007 | `get_cognitive_brief` MCP tool |
| 7 | Пилот получает персонализированный ответ | Пилот | Telegram bot |

## Сценарии использования

**Сценарий 1 (Навигатор, утренний диалог).** Пилот пишет «Навигатор, что мне сегодня делать?» Навигатор читает brief: `orchestrator_brief` = «тяжёлый день по календарю», `tailor_recommendation` = «15-минутная рефлексия вместо полноценного занятия», `stuck_analysis` = null. Ответ: «Сегодня лёгкий день — 15 минут на рефлексию, без давления.»

**Сценарий 2 (Навигатор, пилот застрял).** Пилот не заходил 3 дня. `stuck_analysis` = «пропущен слот 3 дня, WP #47 завис 5 дней». `orchestrator_brief` = «slot_miss». Навигатор: «Вижу, что ты пропустил несколько дней. Давай небольшой шаг — 10 минут на WP #47, только открыть и посмотреть.»

**Сценарий 3 (Пилот без text_analysis consent).** Пилот дал только `activity_tracking` consent. `cognitive_profile` = null. Навигатор получает `orchestrator_brief` + `tailor_recommendation` + `stuck_analysis` и адаптирует ответ на основе поведенческих данных, без когнитивного профиля.

## Связь с другими обещаниями

- Потребляет: **DP.SC.142** (Текстовый анализ — cognitive_profile), **DP.SC.004** (Knowledge Entry — captures), **DP.SC.020** (Event Ingest — события), **DP.SC.107** (Персональное руководство / Портной)
- Используется в: **DP.SC.142** (Навигатор как потребитель text_analysis через brief)
- Ограничение: Никогда не заменяет **DP.SC.091** (Детерминированный профиль). Два независимых канала — brief для рекомендаций, DT для расчётов.
