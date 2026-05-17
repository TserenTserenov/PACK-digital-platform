---
id: DP.SC.137
name: Rewards Analytics (аналитика начислений и прогноз скидок для команды)
type: sc
status: draft
layer: L2-Platform
summary: "Команда (R5 CRM/админ платформы) видит динамику начислений баллов, активные балансы по сегментам пилотов и ожидаемую нагрузку на платформу от конвертации баллов в скидки — без SQL, через Метабазу."
consumer: R5 CRM/админ платформы (Ильшат после WP-281), R2 Архитектор правил, основатель (бизнес-метрики)
created: 2026-05-17
updated: 2026-05-17
related:
  realizes: [WP-311]
  uses: [DP.SC.122, DP.ECON.001]
  complementary: [DP.SC.136, DP.SC.138, DP.SC.105]
  source: "WP-311 Ф8 IntegrationGate 17 мая (новое обещание после probe + диагностики)"
---

# DP.SC.137 — Rewards Analytics

## Обещание

**Кому:** R5 CRM/админ платформы (Ильшат, в процессе передачи по WP-281), R2 Архитектор правил (Тсерен / основатель), будущий маркетинг-аналитик.

**Зачем:** до этого данные о начислениях лежали в `rewards.applied_events` и `rewards.point_balances`, но без агрегации/визуализации. Вопросы стейкхолдеров — «сколько баллов начислили на прошлой неделе?», «сколько активных балансов у нас?», «какой ожидаемый объём скидок в следующем месяце?» — требовали SQL-запросов. Это блокировало (а) ежедневное операционное управление, (б) калибровку правил (нет данных — нет осознанного решения «удвоить ли коэффициент за рефлексию»), (в) финансовое планирование (баллы конвертируются в скидки, нужно прогнозировать). Паттерн 1 Агроскина: стейкхолдер-администратор имеет собственные ожидания, и они = «я знаю, сколько скидок придётся дать».

**Что получит потребитель:**
- **Дашборд «Rewards Overview» в Метабазе** с 5 базовыми вью:
  1. **Динамика начислений:** events_count + sum(effective) по дням за последние 30 дней, разбивка по `activity_domain` (learning / practice / work).
  2. **Активные балансы:** количество accounts с points > 0 + median balance + распределение по quantile (топ-10%, 25%, медиана, нижние 25%).
  3. **Прогноз выплат:** ожидаемая денежная нагрузка = SUM(point_balances.points) × конверсионный коэффициент (1 балл = ₽0.5 текущий) × ожидаемая доля конвертации (исторически 5-20%, параметр view).
  4. **Топ-пилоты по балансу:** N=20, с динамикой за неделю (delta points) — для retention-фокуса.
  5. **Cap-truncation rate:** доля events с `cap_truncated = true` от total applied за неделю — индикатор «правила слишком щедрые или daily_cap слишком жёсткий».
- **Срез по сегментам:** фильтр по `qualification_level` (Ученик / Работник / Стратег / …) + по `student_stage` (для уровня 4, 1-5) — видеть как ведут себя разные сегменты.
- **Анти-PII контракт:** в дашборде НЕТ email/telegram_id/payment_credentials; account_id показывается как `ory_sub[:8]` (первые 8 chars UUID для идентификации без exposure).
- **Refresh:** Метабаза queries — live (по запросу), кэш ≤5 мин.

## Критерий приёмки

1. **Time-to-answer:** ответ на «сколько баллов начислили на прошлой неделе?» — ≤30s от открытия дашборда до видимого числа.
2. **Self-service:** Ильшат / R5 CRM может ответить на 5 базовых вопросов без SQL и без обращения к разработчику. Sample-test после deploy.
3. **Coverage:** дашборд покрывает 7-day window (горизонт операционных решений) и 30-day window (горизонт правил-ревизии). 90-day — отдельная вью с lazy refresh.
4. **PII-граница:** ни одна вью не выводит `email`, `telegram_id`, `phone`, `payment_method_kind`, `payment_credentials`. Audit query доступен в Pack-документации (`DP.ARCH.004 §2 П6.1`).
5. **Forecast accuracy:** прогноз выплат на следующие 30 дней с погрешностью ±30% (calibration через ретроспективу за 90 дней). Не финансовый прогноз с точностью копеек, а sanity-check «не вылетим ли в красное».
6. **Cap-truncation alert:** если cap_truncated rate за неделю > 50% → автоматическая annotation в дашборде «правила слишком щедрые или cap слишком жёсткий», ссылка на DP.SC.138 для симуляции изменения.

## Архитектура

```
┌──────────────────────────────────────────────────────────────┐
│ Метабаза (peaceful-vision Railway)                           │
│                                                              │
│  Dashboard «Rewards Overview»:                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
│  │ Daily       │  │ Active      │  │ Forecast    │           │
│  │ accruals    │  │ balances    │  │ payouts     │           │
│  │ (30d)       │  │ distribution│  │ (next 30d)  │           │
│  └─────────────┘  └─────────────┘  └─────────────┘           │
│  ┌─────────────┐  ┌─────────────┐                            │
│  │ Top pilots  │  │ Cap-trunc   │                            │
│  │ (N=20)      │  │ rate (7d)   │                            │
│  └─────────────┘  └─────────────┘                            │
└────────────┬─────────────────────────────────────────────────┘
             │ JDBC read-only (metabase_reader role)
             ▼
┌──────────────────────────────────────────────────────────────┐
│ Neon rewards DB                                              │
│   ├ rewards.applied_events (audit trail)                     │
│   ├ rewards.point_balances (current state)                   │
│   └ JOIN с reference.qualification_level (FDW)               │
│              + reference.student_stage_multipliers (FDW)     │
│                                                              │
│ ROLE: metabase_reader (GRANT SELECT only)                    │
└──────────────────────────────────────────────────────────────┘
```

## Out of scope

- **Real-time alerting** (TG-пинг команде при аномалии) — это [DP.SC.123 Platform Observability](./DP.SC.123-platform-observability.md), не business analytics. Граница: SC.137 = bizmetrics для команды (опросом), SC.123 = infrastructural alerts.
- **A/B тестирование правил** — нужен симулятор ([DP.SC.138 Rewards Rules Simulation Lab](./DP.SC.138-rewards-rules-simulation-lab.md)), не дашборд.
- **Пилот-facing аналитика** (= [DP.SC.136 Rewards Transparency](./DP.SC.136-rewards-transparency.md)) — другой потребитель.
- **Финансовый dashboard платформы в целом** (revenue / churn / LTV) — отдельный SC после WP-228 cut-over subscription.

## Режимы отказа

| Сбой | Поведение | Восстановление |
|------|-----------|----------------|
| Metabase недоступна | Команда временно через прямые SQL (документировано в `inbox/runbooks/`) | Railway restart, ≤5 мин |
| rewards БД недоступна | Метабаза кеш ≤5 мин, потом «No data» в вью | Auto-recovery |
| metabase_reader без GRANT на новые таблицы | Вью «No data» (Metabase не падает) | Manual GRANT + reload |
| `qualification_level` FDW broken | Вью без segmentation работают; segmented — «Unknown» bucket | FDW fix через 215-sync-fdw-credentials |

## Метрики успеха (для будущего ревью)

- **Adoption:** дашборд открывается командой ≥3 раз/неделю (Metabase view stats).
- **Decisions backed by data:** доля решений «изменить правило / выпустить промо-скидку» с указанием view-snapshot в обосновании — целевой ≥70%.
- **Support time reduction:** время ответа на вопрос «сколько у нас активных балансов?» — было SQL-запрос ~10 мин, целевой ≤30 сек.

## Связи

- **Реализует:** [WP-311 Ф8](../../../../DS-my-strategy/inbox/WP-311-points-realtime-emitter.md) аналитика для администратора
- **Использует данные от:** [DP.SC.122 Rewards Projection](./DP.SC.122-rewards-projection.md), [DP.ECON.001 Points Engine](../02-domain-entities/DP.ECON.001-points-engine.md)
- **Парные обещания:** [DP.SC.136 Rewards Transparency](./DP.SC.136-rewards-transparency.md) (тот же data plane, другой потребитель — пилот), [DP.SC.138 Rewards Rules Simulation Lab](./DP.SC.138-rewards-rules-simulation-lab.md)
- **Бизнес-обещание:** [DP.SC.105 Reputation Economy](./DP.SC.105-reputation-economy.md)
- **Связано с:** [DP.SC.115 Marketing](./DP.SC.115-marketing.md) (отдельный набор маркетинг-метрик; rewards-аналитика = product/economy слой), [DP.SC.009 Analytics and Metrics](./DP.SC.009-analytics-and-metrics.md) (общий dashboard-фреймворк)
- **Исполнитель:** Metabase service (peaceful-vision Railway) + Neon rewards БД с metabase_reader role
