---
id: DP.M.043
name: "Жизненный цикл генерируемых артефактов: явный archive-шаг с retention-окном"
type: method
status: active
valid_from: 2026-05-14
source: "WP-149 Ф-feedback-loop, DS-autonomous-agents commit d34cc88"
---

# DP.M.043: Жизненный цикл генерируемых артефактов — явный archive-шаг с retention-окном

## Проблема

Без archive-шага папка `daily/` копится → агент при следующем render читает все исторические файлы как «текущие» → context window раздувается + устаревший HorizonContext засоряет daily следующего дня.

## Паттерн

При каждом периодическом render-событии: сначала archive → затем creation.

## Шаги

1. **Определить retention-окно** (пример: «текущая неделя», «последние 7 дней»)
2. **Вызвать archive-функцию** при render-событии (weekly render, day-open)
3. **archive-функция** перемещает артефакты older than retention window из `daily/` в `history/`
4. **Читалка** работает по `history/` в retention-окне: ищет `*-reflection.md` за 7 дней

## Инварианты

- Archive-шаг предшествует creation-шагу (не post-creation)
- Retention-окно явно задано в коде (не «всё кроме последнего»)
- Archive ≠ delete: `history/` сохраняет файлы; feedback-loop агента читает из `history/`

## Тест

Убей процесс после archive-шага → подними → reflection-reader находит ожидаемые файлы в `history/` = паттерн применён корректно.

## Применимо

Personal-guide-render, любой агент с rolling-window контекстом: weekly plan archives, session log rotation, email digest history. Порог применения: >14 артефактов одного типа ИЛИ ожидается накопление за >1 месяц.

## Связи

- Реализован в: `DS-autonomous-agents/personal_guide_agent.py` (archive_old_daily)
- Используется с: AS.M.016 (Reflection-driven daily personalization) — рефлексии перемещаются в history/, не теряются
