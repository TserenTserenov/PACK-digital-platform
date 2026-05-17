---
id: DP.SC.138
name: Rewards Rules Simulation Lab (симулятор «что если» для калибровки правил)
type: sc
status: draft
layer: L2-Platform
summary: "R2 Архитектор правил может за 5 минут получить ответ «что бы получили пилоты при таком наборе правил» — без деплоя, на исторических данных. Калибровка перед выкаткой."
consumer: R2 Архитектор правил (Тсерен / основатель), R5 CRM (для обоснования промо-кампаний)
created: 2026-05-17
updated: 2026-05-17
related:
  realizes: [WP-311]
  uses: [DP.SC.122, DP.ECON.001]
  complementary: [DP.SC.136, DP.SC.137]
  source: "WP-311 Ф9 IntegrationGate 17 мая (новое обещание после probe + диагностики)"
---

# DP.SC.138 — Rewards Rules Simulation Lab

## Обещание

**Кому:** R2 Архитектору правил (изменяет `reference.reward_rules` и multiplier-таблицы), R5 CRM (планирует промо-кампании «двойные баллы на неделю»).

**Зачем:** до этого изменение правил было одним из трёх:
1. Слепое (на интуиции «коэффициент 1.5 кажется норм») → риск раздать слишком много баллов и обнаружить через 2 недели через дашборд DP.SC.137.
2. Локальное (psql на проде, посмотреть один-два event) → не масштабируется на 40+ правил × десятки тысяч events.
3. Заблокированное (страх «а что если изменю — пропадёт у кого-то») → правила застревают в неоптимальной точке.

Все три ломают цикл «гипотеза → данные → решение». Паттерны 4 и 9 Агроскина: продукт = описание ожидаемого будущего; симуляция = инструмент изменения описания правил **до** того, как оно изменит реальный мир.

**Что получит потребитель:**
- **CLI/Jupyter инструмент** `rewards-sim` с параметрами:
  - `--rules-override <path-to-yaml>` — alternative set of reward_rules (amount, streak_eligible, match_condition)
  - `--multipliers-override <path-to-yaml>` — alternative activity_domain_multipliers / student_stage_multipliers
  - `--from <date>` / `--to <date>` — окно исторических events для replay (default: последние 30 дней)
  - `--account-segment <filter>` — фильтр accounts (например, «только wave-1 пилоты» / «только Ученик stage=2»)
- **Output:**
  - **Сравнение метрик** (current rules vs override) с дельтой:
    - Total points distributed
    - Median balance growth per pilot per week
    - Cap-truncation rate
    - Expected payout (в ₽) на следующие 30 дней
  - **Per-pilot delta** (≤20 топ-движений): кто получил больше / меньше / без изменений
  - **Cap-saturation breakdown:** доля events bumped в cap при new rules vs current
- **Скорость:** один симуляционный run на 30-day window × 5K events × 40 rules — **≤5 минут**.
- **Idempotency:** симуляция = pure-read, никакой записи в prod БД. Sandbox БД (Neon branch) либо in-memory through asyncpg cursor.

## Критерий приёмки

1. **Time-to-insight:** от формулировки гипотезы («что если удвоить amount для git_commit») до видимой дельты — ≤10 мин (включая написание override YAML).
2. **Reproducibility:** один и тот же `--rules-override` + `--from/to` → один и тот же output (deterministic). Snapshot rules + events на момент запуска фиксируется.
3. **Sandbox isolation:** ни один симуляционный run НЕ пишет в `rewards.point_balances`, `rewards.applied_events`, `reference.reward_rules`. Verify через post-run audit query.
4. **Coverage 40 rules:** все 50 enabled правил отрабатываются в симуляции (тест на каноническом наборе events с расчётом expected vs actual).
5. **Useful output:** R2 после run понимает «что меняется» без обращения к коду симулятора. Sample-test с 1-2 гипотезами после deploy.
6. **No accidental prod write:** код симулятора имеет блок `assert dsn != production_dsn` или эквивалент перед любым INSERT/UPDATE. Тест с mock prod DSN — должен fail loudly.

## Архитектура

```
┌──────────────────────────────────────────────────────────────┐
│ Architect (CLI / Jupyter)                                    │
│                                                              │
│   $ rewards-sim --rules-override examples/double-git.yaml \  │
│                 --from 2026-04-17 --to 2026-05-17 \          │
│                 --account-segment 'wave1'                     │
└────────────────────────┬─────────────────────────────────────┘
                         ▼
┌──────────────────────────────────────────────────────────────┐
│ rewards-sim engine (Python, in-memory)                       │
│                                                              │
│   1. fetch_events(learning_dsn READONLY, from, to, segment)  │
│      → 5K-50K DomainEvent objects                            │
│                                                              │
│   2. load_current_rules(reference_dsn READONLY) → 50 rules   │
│      apply_override(yaml) → merged rules dict                │
│                                                              │
│   3. load_multipliers(reference_dsn READONLY)                │
│      apply_override(yaml) → merged multipliers               │
│                                                              │
│   4. For each event ordered by occurred_at:                  │
│      compute_effective_amount_in_memory(                     │
│        rules, multipliers, event, account_state)             │
│      → simulated_delta                                       │
│      account_state.balance += simulated_delta                │
│      account_state.daily_cap_used += simulated_delta         │
│                                                              │
│   5. Aggregate: total / median / cap-rate / payout-forecast  │
│                                                              │
│   6. Diff vs current snapshot (загружен из prod без override)│
│                                                              │
│   7. Print Δ-report + сохранить CSV/JSON в sandbox/runs/     │
└──────────────────────────────────────────────────────────────┘
```

## Out of scope

- **Прогноз через ML** (regression на основе истории) — это статистическое моделирование, отдельный SC. Симулятор = deterministic replay по новым rules.
- **A/B тестирование на живых пилотах** — несовместимо с обещанием транспарентности (DP.SC.136). Если хочется реальный A/B — отдельный РП с consent-gate.
- **Auto-tuning** (LLM/optimizer предлагает rules) — высокий риск (rules влияют на пользовательский опыт + финансы), не на этом этапе.
- **Real-time recompute** на проде — это и есть текущий pipeline (DP.SC.122). Симулятор = sandbox, не реактор.

## Режимы отказа

| Сбой | Поведение | Восстановление |
|------|-----------|----------------|
| readonly DSN недоступен | run fails fast («cannot connect to learning/reference») | retry / smaller window |
| OOM на больших windows (>180 days) | Streaming через chunks по 10K events; metric memory_high_water | --chunk-size override |
| Случайный prod DSN в override | Refuse to run (assert + clear error) | Manual check ENV |
| reward_rules schema drift | Override валидируется через JSON Schema (как event_schemas) | Update override после migration |

## Метрики успеха

- **Usage:** Архитектор запускает ≥1 симуляцию перед каждым изменением правил production — целевой ≥80% после 2 недель.
- **Decision quality:** доля post-change правил, где actual delta (через DP.SC.137 view) укладывается в forecast ±30% — целевой ≥70%.
- **Prevented incidents:** число «отозванных» изменений правил (rolled back в течение 7 дней) — целевой −50% vs до симулятора.

## Связи

- **Реализует:** [WP-311 Ф9](../../../../DS-my-strategy/inbox/WP-311-points-realtime-emitter.md) лаборатория симуляций
- **Использует данные от:** [DP.SC.122 Rewards Projection](./DP.SC.122-rewards-projection.md), [DP.ECON.001 Points Engine](../02-domain-entities/DP.ECON.001-points-engine.md) (формула)
- **Парные обещания:** [DP.SC.136 Rewards Transparency](./DP.SC.136-rewards-transparency.md), [DP.SC.137 Rewards Analytics](./DP.SC.137-rewards-analytics.md)
- **Бизнес-обещание:** [DP.SC.105 Reputation Economy](./DP.SC.105-reputation-economy.md)
- **Не путать:** [WP-319 Simulator Lab](../../../../DS-my-strategy/archive/wp-contexts/) — другая лаборатория, симулирует характеристики Созидателя/ступени, не reward rules.
- **Исполнитель:** Python CLI/Jupyter в новом репо `DS-IT-systems/rewards-sim` (создаётся при реализации Ф9, W22+).
