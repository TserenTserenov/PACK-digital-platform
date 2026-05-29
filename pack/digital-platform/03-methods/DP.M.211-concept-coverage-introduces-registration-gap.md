---
id: DP.M.211
name: "Диагностика L1 FAIL в concept-coverage по регистрационному зазору"
type: method
domain: digital-platform
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-transcript 2026-05-28, WP-362-Ф3"
---

# DP.M.211 Диагностика L1 FAIL в concept-coverage по регистрационному зазору

## Вход

Список концептов с FAIL/WARN в отчёте concept-coverage lint.

## Алгоритм

1. grep концепта по директории целевого guide (рекурсивно)
2. Если вхождений нет → контент-проблема (концепт не введён в тексте)
3. Если вхождения есть → диагноз меняется: регистрационный зазор
4. Найти первое определение концепта (критерии: жирный+двоеточие / «X — это Y» / заголовок подраздела содержит концепт)
5. Если определение найдено → добавить концепт в `introduces` frontmatter соответствующего файла
6. Если ни одно из трёх условий не выполнено → `deferred` с письменным explanation

## Выход

`introduces` поле обновлено; или decision documented с explanation.

## Инвариант

L1 FAIL при наличии концепта в тексте = не пропущенный контент, а незарегистрированный `introduces`.

## Применимость

Любые knowledge-base системы с manifest-файлами и концепт-трекингом.

## Связи

- Failure mode: DP.FM.103 coverage-скрипт без фильтра scope
- Источник: WP-362 Ф3, 2026-05-28
