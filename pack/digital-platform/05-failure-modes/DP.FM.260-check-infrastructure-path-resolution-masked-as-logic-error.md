---
name: Check infrastructure path resolution error masked as logic error
id: DP.FM.260
domains: [agent-checks, protocol-infrastructure, day-open]
tags: [false-positive, file-resolution, check-infrastructure]
severity: medium
---

# DP.FM.260 — Ошибка разрешения пути в инфраструктуре проверок маскируется под нарушение логики

## Суть

Проверка (check) падает не потому что правило нарушено, а потому что не найден или неверно выбран исходный файл (path resolution error) или неверно разобран его формат. Слой разрешения пути не отделён от слоя логики проверки. При падении нельзя отличить «правило нарушено» от «файл не там, где ищем».

## Инцидент

Day Open 2026-07-11 (WP-7, git commit ee1ece9): три ложных провала/пропуска проверок. Проверка искала `current/Plan W{N}` в главной директории `~/IWE/`, а план лежал в `DS-my-strategy/current/`. Результат: false-fail вместо PASS.

## Fix

- Отделить path-resolution от логики проверки: явная функция `resolve_check_source(repo, mode)` с логированием
- При падении проверки различать: «файл не найден по пути X» vs «правило нарушено: найдено Y, ожидалось Z»
- Для каждого репо/режима документировать каноническое местонахождение файлов-источников

## Паттерн

При добавлении новой проверки: (1) явный шаг разрешения пути с логированием, (2) guard на «файл не найден» отдельно от «проверка провалилась», (3) тест кейс «файл есть — проверка PASS» + «файл не найден — resolution error, не fail».

**Источник:** session-close 2026-07-11 (git commit ee1ece9, extensions/day-open.checks.md).
**Смежно:** [DP.FM.087] (watchdog false positive), [DP.FM.031] (hardcoded OS path), WP-7 (техдолг инфраструктуры проверок).
