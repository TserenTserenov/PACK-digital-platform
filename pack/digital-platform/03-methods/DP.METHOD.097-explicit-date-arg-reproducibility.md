---
id: DP.METHOD.097
type: method
domain: PACK-digital-platform
status: draft
summary: "Скрипт-генератор с date-dependent output принимает дату явно как --date YYYY-MM-DD. datetime.now() как единственный источник даты запрещён: делает вывод нетестируемым и ретроактивный рендеринг невозможным."
created: 2026-07-01
trust:
  F: 3
  G: domain
  R: 0.88
epistemic_stage: evidence
related:
  see_also: [DP.M.023, DP.M.044]
tags: [generation, scripting, reproducibility, date, testing, determinism, rendering]
wp: WP-149
---

# Explicit --date arg для воспроизводимости генерации (DP.METHOD.097)

## Описание

Любой скрипт-генератор или рендерер, производящий output зависящий от текущей даты, принимает дату как явный параметр командной строки.

## Проблема со скрытым datetime.now()

Скрипт с `datetime.now()` внутри:
- Нетестируем для прошедших дат
- Не поддерживает ретроактивное восстановление пропущенных дней
- Даёт разные результаты при каждом запуске (не воспроизводим)

## Паттерн

```python
parser.add_argument('--date', type=str, help='Render date (YYYY-MM-DD), default: today')
render_date = args.date or datetime.now().strftime('%Y-%m-%d')
```

Scheduled-job явно передаёт целевую дату:
```bash
python render.py --date "$(date +%Y-%m-%d)"
```

## Применение

- Рендеры персональных руководств, дневных отчётов
- Любой pipeline с date-dependent output (дайджесты, снапшоты)
- CI/CD jobs с temporal dependencies

## Инвариант

Дефолт = «сегодня», явный аргумент переопределяет. Это позволяет:
1. Ретроактивный рендеринг пропущенных дней
2. Детерминированное тестирование (конкретная дата = конкретный output)
3. Scheduled-job явно передаёт свою дату-цель
