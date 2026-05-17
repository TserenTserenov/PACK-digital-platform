---
id: DP.M.045
name: "Три оси Service Clause автоматизированного процесса"
type: method
status: active
valid_from: 2026-05-15
source: "WP-FMT session-close 2026-05-15; SC for cron/timer auto-process"
related:
  - DP.D.058
  - DP.D.039
---

# DP.M.045 — Три оси Service Clause автоматизированного процесса

## Описание

Service Clause для автоматизированного процесса (cron, timer, event-driven) содержит три обязательные оси, отсутствующие или неявные в SC интерактивных сервисов.

## Три оси

| Ось | Вопрос | Пример |
|-----|--------|--------|
| **Триггер** | Что запускает процесс? | cron `0 4 * * *` / event `day_close` / manual |
| **Периодичность** | Как часто? | ежедневно, еженедельно, по событию (idempotent?) |
| **Режим исключения** | Что происходит при сбое или пропуске? | retry 3× / skip + alert / manual trigger |

## Когда применять

При написании SC для:
- cron-воркеров (нотный агент, overnight-auditor)
- event-triggered процессов (inbox-check при day-open)
- scheduled agents (weekly guide render)

## Отличие от интерактивного SC

Интерактивный SC: `trigger = явный запрос потребителя`.
Автоматизированный SC: `trigger = расписание / событие системы`.

Режим исключения критичен для автоматизированных процессов — без него неясно что делать при сбое в 04:45 утра без наблюдателя.

## Шаблон (дополнение к стандартному SC)

```yaml
trigger: "cron 0 4 * * * (МСК) / event: day_close"
frequency: ежедневно (idempotent при повторном запуске)
failure_mode: "retry 1×, при fail → alert в TG + skip до следующего дня"
```

## Связи

- DP.D.058 (SC ≠ Carrier) — три оси описывают SC, не реализацию (carrier)
- DP.SC.131 — пример автоматизированного SC с тремя осями (cron/timer автопроцесс)
