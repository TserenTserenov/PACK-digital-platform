---
id: DP.FM.109
name: Sentinel empty-string → прошлый слот планировщика
type: failure-mode
domain: digital-platform
prefix: DP
trust: evidence-based
epistemic_stage: established
valid_from: 2026-05-29
source: DS-my-strategy/inbox/captures.md (session WP-357, 2026-05-29)
---

# DP.FM.109 — Sentinel empty-string → прошлый слот планировщика

## Описание

Планировщик хранит `last_run=''` как sentinel «никогда не запускался». Функция `expected_next_run(last_run)` интерпретирует пустую строку как epoch-zero → вычисляет прошедший слот → watchdog немедленно сигнализирует «overdue» для всех новых процессов.

## Симптом

Watchdog выдаёт false alarms с первого запуска. Процессы, запущенные впервые, сразу помечаются как опоздавшие. Мониторинг «работает», но выдаёт ложные данные без видимой ошибки.

## Механизм

```
last_run='' → epoch-zero → expected_next_run() возвращает прошлый слот → overdue=True → false alarm
```

## Исправление

Явная проверка пустого значения:
```python
if last_run == '':
    return next_from_now()
```

## Применимость

Любой планировщик с состоянием «never-run» (sentinel-значение). Аналог: explicit `None` check в datetime расчётах. Особенно опасен при cold-start: система выглядит работающей, но выдаёт ложные данные.

## Связи

- Паттерн аналогичен DP.FM.100 (stale-snapshot), но на уровне scheduler state, не DB snapshot