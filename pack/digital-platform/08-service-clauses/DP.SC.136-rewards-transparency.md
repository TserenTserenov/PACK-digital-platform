---
id: DP.SC.136
name: Rewards Transparency (понимание пилотом источника своих баллов)
type: sc
status: draft
layer: L2-Platform
summary: "Пилот видит не просто число «у тебя X баллов», а понятную причинно-следственную цепочку: за что начислено, сколько по каждому правилу, какие правила игры действуют сейчас."
consumer: Пилоты-волонтёры программы ЛР (wave-1+), будущие платные участники
created: 2026-05-17
updated: 2026-05-17
related:
  realizes: [WP-311]
  uses: [DP.SC.122, DP.ECON.001, DP.ROLE.034]
  complementary: [DP.SC.137]  # Аналитика для админа — другой потребитель тех же данных
  source: "WP-311 Ф7 IntegrationGate 17 мая (новое обещание после probe + диагностики)"
---

# DP.SC.136 — Rewards Transparency

## Обещание

**Кому:** пилотам программы личного развития (wave-1 ≈ 5-6 человек, wave-2 +15, до 50 к концу мая), а позднее — платным участникам.

**Зачем:** до этого `/points` возвращал только число. Пилот видит «у меня 1410 баллов», но не понимает откуда — это вызывает три типа реакций: (1) «случайное число, не моё», (2) «значит можно ничего не делать, всё равно растёт», (3) обращение в поддержку. Все три ломают мотивационный контур: баллы должны быть обратной связью на действие, а число без объяснения обратной связью не является. Паттерн 7 Агроскина: обещание = понятное будущее состояние мира, не «мы что-то считаем».

**Что получит потребитель:**
- На запрос `/points` в боте — структурированный ответ из трёх блоков:
  1. **Текущий баланс** (что есть сейчас, плюс прирост за сегодня).
  2. **За что начислено** — последние N (≥5) событий с разложением: «закрытие дня → 30 баллов», «коммит в PACK-personal → 90 баллов (потолок дня сработал)». Видны cap_truncated случаи как пометка «потолок».
  3. **Правила игры** — короткая инфо «что приносит баллы»: типы действий, базовые суммы, как работает потолок дня и серия (streak), как меняется множитель по ступени.
- Команда `/rules` в боте — расширенная справка по правилам (40+ правил с amount, streak_eligible, описанием).
- Команда `/points history` (опционально, в Ф7.2) — лента начислений за месяц с разбивкой по дням.
- Время ответа `/points` — ≤2 секунды (UX-предел для conversational interface).

## Критерий приёмки

1. **Coverage:** пилот после команды `/points` может ответить на вопрос «откуда у меня это число» без обращения в поддержку. Проверка — solo-smoke с 1-2 wave-1 пилотами через 7 дней после wave-2 broadcast.
2. **Latency p95 ≤2s:** на запрос `/points` в боте от tap кнопки до полного ответа в TG ≤2с (включая DB roundtrip + LLM-free format).
3. **Honesty:** если у пилота 0 баллов — `/points` объясняет почему (нет opt-in / нет reward-eligible событий / сегодня пусто). Не молчит, не показывает «0» в вакууме.
4. **Cap visibility:** events с `cap_truncated = true` помечены явно («потолок дня сработал»), чтобы пилот видел что система не «съела» его действие, а ограничение в правиле.
5. **No PII leak:** в детализации событий — только тип действия + источник (repo для git_commit, не commit_message); ни email/telegram_id, ни payment_credentials.
6. **Rules visibility:** `/rules` показывает актуальные `reward_rules` (50 enabled на 17 мая), сгруппированные по типу (учёба / практика / работа), с base_amount и streak-флагом.

## Архитектура

```
┌──────────────────────────────────────────────────────────────┐
│ Пилот в TG → /points                                         │
└────────────────────────┬─────────────────────────────────────┘
                         ▼
┌──────────────────────────────────────────────────────────────┐
│ aist_me_bot (DP.ROLE.X)                                      │
│                                                              │
│   1. SELECT pb.points, pb.last_updated                       │
│      FROM rewards.point_balances pb                          │
│      WHERE pb.account_id = $ory_sub::uuid                    │
│                                                              │
│   2. SELECT ae.event_type, ae.effective, ae.applied_at,      │
│             ae.cap_truncated, ae.base_amount,                │
│             ae.dom_mult, ae.qual_mult, ae.streak_mult        │
│      FROM rewards.applied_events ae                          │
│      WHERE ae.account_id = $ory_sub::uuid                    │
│        AND ae.effective > 0                                  │
│      ORDER BY ae.applied_at DESC                             │
│      LIMIT 5                                                 │
│                                                              │
│   3. Today_total = SUM(effective WHERE DATE(applied_at)=today)│
│                                                              │
│   4. Format: text + TG-кнопка «Правила игры» → /rules        │
└──────────────────────────────────────────────────────────────┘
                         ▼ /rules
┌──────────────────────────────────────────────────────────────┐
│   SELECT trigger_event, amount, streak_eligible, description │
│   FROM reference.reward_rules                                │
│   WHERE reward_kind = 'points'                               │
│     AND (valid_to IS NULL OR valid_to > NOW())               │
│     AND amount > 0                                           │
│   ORDER BY amount DESC, trigger_event                        │
│                                                              │
│   Группировка по category (через _event_type_to_domain):     │
│   - 📚 Учёба (learning)                                      │
│   - 🛠 Практика (practice)                                   │
│   - 💼 Работа (work) — по repo                              │
└──────────────────────────────────────────────────────────────┘
```

## Out of scope

**Не обещаем (этим SC):**
- **Симулятор правил для пилота** («а что если я закрою 3 дня подряд, сколько получу?») — отдельное обещание `DP.SC.138 Rewards Rules Simulation Lab`, потребитель — R2 Архитектор правил, не пилот.
- **Прогноз скидок** («ты накопил баллы, какой это денежный эквивалент») — частично в `/points` summary (1 балл = ₽X), полная экономика — `DP.SC.105 Reputation Economy`.
- **Аналитика поведения для команды** («кто активен / кто стagнирует») — `DP.SC.137 Rewards Analytics`.
- **Push-уведомления о начислениях** — анти-паттерн notification fatigue. Пилот сам спрашивает `/points` когда хочет видеть.

## Режимы отказа

| Сбой | Поведение | Восстановление |
|------|-----------|----------------|
| `rewards.point_balances` пуст для пилота | Сообщение «Баллы не начисляются — нет согласия (opt-in) или ещё нет reward-eligible действий за время сессии. Команды /consent + /day_close дадут первые баллы.» | Сам пилот opt-in + ритуал |
| Latency >2s (DB slowdown) | Loading-индикатор в TG (typing), ответ когда придёт. Не fallback на cached. | Auto-recovery когда rewards БД доступна |
| `applied_events` пуст за 7 дней но balance >0 | Объясняем явно: «исторические события из импорта (LMS, до подключения)». Не паника. | — |
| Rules БД недоступна (для `/rules`) | Cached snapshot (refresh раз в час), показываем «правила обновлены час назад» | Auto-recovery при доступе |

## Метрики успеха (для будущего ревью)

- **Adoption:** доля wave-1 пилотов, использующих `/points` ≥1 раз/неделю — целевой ≥80% через 2 недели после broadcast.
- **Support tickets «откуда баллы»:** целевой 0 от пилотов wave-2 после Ф7 deploy (sample N=15).
- **Cap_truncated visibility:** доля жалоб «куда делись баллы» когда cap_truncated показан явно — целевой ≤5% от total `/points` queries.

## Связи

- **Реализует:** [WP-311 Ф7](../../../../DS-my-strategy/inbox/WP-311-points-realtime-emitter.md) prozrachnost rasčëta dlja polʹzovatelja
- **Использует данные от:** [DP.SC.122 Rewards Projection](./DP.SC.122-rewards-projection.md) (applied_events + point_balances), [DP.ECON.001 Points Engine](../02-domain-entities/DP.ECON.001-points-engine.md) (формула v2)
- **Парные обещания:** [DP.SC.137 Rewards Analytics](./DP.SC.137-rewards-analytics.md) (тот же data plane, другой потребитель — команда), [DP.SC.138 Rewards Rules Simulation Lab](./DP.SC.138-rewards-rules-simulation-lab.md) (Архитектор правил)
- **Бизнес-обещание:** [DP.SC.105 Reputation Economy](./DP.SC.105-reputation-economy.md) (баллы → скидки, экономика)
- **Карта БД:** DP.ARCH.004 v2.3 §3.12 (reader `rewards.point_balances` + `rewards.applied_events` + `reference.reward_rules`)
- **Исполнитель:** `aist_me_bot` (handler команды `/points`, `/rules`) — DS-IT-systems/aist-bot
