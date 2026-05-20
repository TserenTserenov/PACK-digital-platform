---
id: DP.M.100
name: "Vocabulary Sufficiency Gate"
type: method
description: "CI-проверка: каждое понятие, введённое в контент, должно иметь соответствующую запись в Pack-онтологии"
trust: confirmed
epistemic_stage: demonstrated
valid_from: 2026-05-19
---

# DP.M.100 Vocabulary Sufficiency Gate

## Суть метода

Автоматическая проверка в CI: если контент вводит новое понятие (секция «вводится»), в Pack-онтологии (ontology.md) должна существовать соответствующая запись. Предотвращает тихий vocabulary drift — когда руководство оперирует понятиями без Pack-формализации.

## Форматы запуска

- **Полный режим:** проверяет все понятия во всех guide-файлах
- **Diff-режим:** проверяет только новые понятия из файлов, изменённых в PR

## Шаги

1. Определить «vocabulary manifest» Pack'а (ontology.md §2 или эквивалент)
2. Определить «content files» с явными понятиями (structure-guide, *.md с «вводится»)
3. Реализовать check-script: extract(вводится) → compare(ontology) → report(gap)
4. Интегрировать в CI: запуск на PR/push при изменении structure-guide файлов
5. Обработать gap: добавить Pack-запись ИЛИ явно исключить из scope

## Тест применимости

«Есть ли в системе список понятий (glossary/ontology) и контент, который их должен использовать?» Да → vocabulary sufficiency gate применим.

## Связи

- Аналогичный паттерн: DP.M.088 (CI precommit defense-in-depth) — файловый уровень
- Этот метод: семантический уровень (словарный запас vs spec/impl файлы)
- Lint completeness check (DP.M.097) — spec vs impl cross-check (другая ось)

## Прецедент

DS-principles-curriculum WP-322 Ф11 (2026-05-19): `tools/check-pack-gap.py` + `.github/workflows/content-validation.yaml`. MIM.M.022 в PACK-MIM — domain-specific экземпляр того же паттерна.
