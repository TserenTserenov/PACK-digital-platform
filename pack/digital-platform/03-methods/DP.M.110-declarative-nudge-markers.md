---
id: DP.M.110
type: method
name: "Декларативный словарь предикатов для nudge-движка"
domain: nudge-engine
epistemic_stage: empirical
trust: verified
valid_from: 2026-05-20
source: "WP-346 Ф2 (onboarding_controller.py, 24/24 tests)"
---

# DP.M.110 — Декларативный словарь предикатов для nudge-движка

**Паттерн:** Вместо if-else цепочки — декларативный словарь `шаг → (предикат, сообщение)`. Алгоритм: найти наименьший N с выполненным предикатом и незаписанным шагом.

## Структура

```python
MARKERS: dict[int, tuple[Callable[[State], bool], str]] = {
    0: (lambda s: not s.consent, "Разрешить уведомления"),
    1: (lambda s: not s.profile_complete, "Заполнить профиль"),
    # ...N: (predicate_N, message_N),
}

def decide(state: State, cooldown_hours: int = 24) -> int | None:
    if is_cooldown_active(cooldown_hours):
        return None
    for n in sorted(MARKERS):
        pred, _ = MARKERS[n]
        if pred(state) and msg_not_sent(n, state):
            return n
    return None
```

## Три компонента

1. **Словарь предикатов** — каждый шаг = один предикат + одно сообщение; добавление шага = одна строка
2. **Cool-down gate** — `is_cooldown_active()` с настраиваемым периодом (24h по умолчанию): защищает от спама при частых tick'ах (3×/день, cron, event-driven)
3. **Channel-router** — изолирует логику доставки от логики решения:
   - TG push: стандартные шаги
   - render-call: шаги с rich-content (интерактивные блоки)
   - FSM-trigger: шаги с изменением состояния интерфейса

## Когда применять

Любая последовательная nudge-система с фиксированным порядком и условиями:
- Онбординг (последовательные шаги)
- Streak-восстановление (условия возврата)
- Retention-flow (триггеры по бездействию)

## Не применять

- Нелинейные flow (несколько активных веток одновременно) → FSM или state machine
- Персонализированный контент без предиката (A/B-тестирование) → другой паттерн

## Прецедент

WP-346 Ф2: 11 predicate-entries для msg 0–10, 3 edge-кейса (нет триггера, cool-down блокирует, без consent) → 24/24 unit-тесты.

## Связи

- **see_also:** DP.SC.135 (Agent Inbox) — для входящих agent-задач
- **see_also:** DP.M.079 (State-machine) — для сложных нелинейных переходов
- **source_wp:** WP-346
