---
id: AGT.012.SCENARIOS
type: scenarios-index
status: draft
created: 2026-02-07
---

# Сценарии StrategicPlanner

## Обзор

Агент StrategicPlanner работает в двух режимах:
1. **По расписанию (scheduled)** — автоматический запуск в определённое время
2. **По запросу (on-demand)** — запуск пользователем

---

## Временная сетка

```
           Пн          Вт-Сб        Вс
7:00    strategy-     day-plan    day-plan
        session
        ↓
        day-plan

22:00      —            —         week-review

Последний день месяца: month-report
```

---

## Сценарии по расписанию (4 шт.)

| # | Сценарий | Файл | Триггер | Выход |
|---|----------|------|---------|-------|
| 1 | Strategy Session | [01-strategy-session.md](scheduled/01-strategy-session.md) | 7:00 Пн | План недели + сдвиг месяца |
| 2 | Day Plan | [02-day-plan.md](scheduled/02-day-plan.md) | 7:00 ежедневно | План дня + апдейт вчера |
| 3 | Week Review | [03-week-review.md](scheduled/03-week-review.md) | 22:00 Вс | Итоги недели (для клуба) |
| 4 | Month Report | [04-month-report.md](scheduled/04-month-report.md) | Последний день месяца | Отчёт за месяц |

---

## Сценарии по запросу (4 шт.)

| # | Сценарий | Файл | Триггер | Выход |
|---|----------|------|---------|-------|
| 1 | Evening Review | [01-evening-review.md](on-demand/01-evening-review.md) | Конец дня | Итоги дня |
| 2 | Check Plan | [02-check-plan.md](on-demand/02-check-plan.md) | При получении задачи | Сверка с планом |
| 3 | Update Priorities | [03-update-priorities.md](on-demand/03-update-priorities.md) | В любой момент | Изменённый план |
| 4 | Add Work Product | [04-add-workproduct.md](on-demand/04-add-workproduct.md) | При новом РП | РП добавлен в план |

---

## Поток данных

```
                    ┌─────────────────┐
                    │ monthly-        │
                    │ priorities.md   │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         ↓                   ↓                   ↓
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ strategy-       │  │ week-review     │  │ month-report    │
│ session (Пн)    │  │ (Вс 22:00)      │  │ (конец месяца)  │
└────────┬────────┘  └────────┬────────┘  └─────────────────┘
         │                    │
         ↓                    │
┌─────────────────┐           │
│ weekly-plan.md  │←──────────┘
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ day-plan        │
│ (7:00)          │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ daily/          │
│ YYYY-MM-DD.md   │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│ evening-review  │
│ (по запросу)    │
└─────────────────┘
```

---

## Slash-команды (Claude Code)

После описания сценариев будут созданы команды:

| Команда | Сценарий |
|---------|----------|
| `/day-plan` | day-plan |
| `/strategy` | strategy-session |
| `/evening` | evening-review |
| `/week-review` | week-review |
| `/month-report` | month-report |
| `/check-plan` | check-plan |
| `/update-priorities` | update-priorities |
| `/add-wp` | add-workproduct |

---

## Связанные документы

- [Паспорт агента](../00-agent-passport.md)
- [Шаблоны](../templates/)
