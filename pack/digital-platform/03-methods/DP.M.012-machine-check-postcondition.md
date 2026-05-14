---
id: DP.M.012
name: Machine-Check Postcondition
kind: Method
status: active
created: 2026-05-08
sources:
  - FMT-exocortex-template commit c3b1100 (2026-05-08)
  - WP-297 ОПТ-5
---

# DP.M.012: Machine-Check Postcondition

## Определение

Паттерн обеспечения качества чеклиста: любой шаг, записывающий артефакт (файл, запись, коммит), обязан содержать машинную проверку (grep/test) как postcondition.

**Инвариант:** postcondition FAIL → шаг НЕ помечается выполненным; исполнитель возвращается к шагу записи.

## Алгоритм применения

1. Шаг чеклиста содержит действие записи (Write / Edit / commit / архивация)
2. Немедленно после — grep-postcondition по имени файла или ключевому содержимому
3. FAIL (grep ничего не находит) → вернуться к записи, не помечать ✓
4. PASS → помечать шаг выполненным

## Примеры

- **Day Close 9a** (архивация DayPlan): grep проверяет наличие файла в archive/
- **Day Close 9b** (запись итогов в WeekPlan): grep проверяет ключевую фразу в WeekPlan
- **KE-применение**: после создания Pack-файла — grep по имени файла в целевой директории

## Почему важно

Silent skip: шаг помечается выполненным, но файл не создан / не обновлён → накопленный drift (WeekPlan без итогов, DayPlan не архивирован). Машинная проверка исключает этот класс ошибок.

## Связи

- Применено: FMT-exocortex-template Day Close SKILL.md (шаги 9a, 9b)
- Антипаттерн: DP.FM.011 (not capturing patterns)
- Контекст: WP-297 ОПТ-5
