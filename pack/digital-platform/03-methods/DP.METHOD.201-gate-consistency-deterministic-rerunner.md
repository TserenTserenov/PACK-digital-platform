---
id: DP.METHOD.201
name: "Gate-consistency + детерминированный rerunner для автономного протокола"
type: method
status: active
valid_from: 2026-07-10
source: "peer-session 2026-07-10-26-self-healing-day-open; DS-my-strategy commit c639cd6d9"
summary: "Идемпотентный scheduled protocol: перед запуском проверить существование артефактов (gate-consistency), при повторном прогоне с теми же входными — тот же результат без дублирования."
tags: [idempotency, scheduled-protocol, gate-consistency, rerunner, autonomous, self-healing]
---

# DP.METHOD.201 — Gate-consistency + детерминированный rerunner для автономного протокола

## §0 Проблема

Scheduled autonomous protocol (launchd, cron, systemd-timer) может быть прерван (сеть, сбой) и перезапущен. Без защиты: второй запуск создаёт дублированные артефакты или ломает уже созданные.

## §1 Два принципа

**Gate-consistency (перед запуском):** ДО выполнения протокол проверяет, что его ключевые артефакты ещё не существуют. Если уже есть → exit 0, без ошибки.

```bash
# Пример: автономный Day Open
if [ -f "$DAYPLAN_PATH" ]; then
    echo "DayPlan already exists, skipping" && exit 0
fi
# ... generate DayPlan ...
```

**Deterministic rerunner (при выполнении):** повторный прогон с теми же входными не создаёт дублей и не ломает уже созданные артефакты. Каждый шаг: «уже сделано?» перед выполнением.

## §2 Совмещение

```
launchd/cron → gate-check → артефакт существует? → exit 0 (skip без ошибки)
                          ↓
                   нет артефакта → deterministic run → exit 0
                          ↓
             случайный повторный запуск → gate-check → exit 0 (skip)
```

Второй запуск безвреден: gate-check поймает уже созданный артефакт → skip.

## §3 Инварианты

- Gate-check только читает файловую систему (side-effect-free)
- Каждый шаг rerunner использует create-if-not-exists, не overwrite
- Exit code = 0 для skip и для success (scheduler не считает skip ошибкой)

## §4 Применимость

Любой scheduled autonomous protocol с ORZ-структурой: Day Open, Day Close, Week Close, batch jobs. Без idempotency: retry = новая копия состояния → дублированные артефакты.

## §5 Связи

- DP.METHOD.058 (replay-and-fork): идемпотентность в другом контексте (replay агентских решений)
