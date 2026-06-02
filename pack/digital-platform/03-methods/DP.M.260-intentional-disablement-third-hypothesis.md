---
id: DP.M.260
name: "Intentional disablement как третья гипотеза при пустой/нулевой функции"
type: method
domain: digital-platform
pack_refs: [DP.FM.106]
status: active
valid_from: 2026-05-30
schema_version: 1
source: "peer-session 2026-05-30-17-pulse-daily-report-zeros (report.md Тема 1, эволюция позиций)"
---

# DP.M.216 Intentional disablement как третья гипотеза

## Проблема

Стандартный debugging оперирует двумя гипотезами:
- **Never-finished:** «никогда не работало»
- **Regression:** «работало, сломалось»

Третья гипотеза часто пропускается: **intentional disablement** — раньше работало, кто-то заглушил намеренно (P0-fix в инциденте, schema-change ломал → отключили запросы, perf issue → закомментили SQL).

## Признаки intentional disablement

- Код выглядит «как огрызок» — присутствует структура (dict, функция), но без реального запроса
- История показывает revert/stub-commit в недавнем прошлом
- Коммит-сообщение содержит «temp», «stub», «disabled», «rotation», «migration»

## Метод

При обнаружении never-finished — **обязательный шаг ДО выбора стратегии fix'а:**

```bash
git log --all -p <file>
git blame <line>
```

Если найден stub-commit → fix = восстановить из истории / порт из аналога (см. метод порта working SQL), **не** реимплементить с нуля.

## Антипаттерн

«Не работало → давай напишу заново» при существовании рабочей версии до stub'а → потеря edge-case логики, regressions через 1-2 спринта.

## Применимость

- Legacy-функции с подозрительно пустым телом
- P0-fix forensics
- Аудит «почему этот метод пустой»
- Любая ситуация «metric=0 без объяснения»

## Связи

- DP.FM.106 (Маскировка нулей вместо root-fix) — FM-сторона того же класса