---
id: DP.SOTA.017
name: Концептуальные графы — мировой опыт
type: sota
status: active
summary: "Паттерны управления knowledge graphs: orphan-prevention, центральные узлы, многоязычность, editorial pipeline. Источники: OBO Foundry, Microsoft GraphRAG, Knowledge Space Theory (ALEKS), Wikidata."
created: 2026-04-15
valid_from: 2026-04-15
trust:
  F: 3
  G: sota
  R: 0.8
related:
  informs: [DP.IWE.001, WP-242]
  uses: []
sources:
  - https://obofoundry.org/principles/fp-000-summary.html
  - https://microsoft.github.io/graphrag/index/default_dataflow/
  - https://www.aleks.com/about_aleks/knowledge_space_theory
  - https://www.wikidata.org/wiki/Wikidata:Introduction
---

# DP.SOTA.017: Концептуальные графы — мировой опыт

> Исследование выполнено в рамках WP-242 (Ф0). Цель: найти применимые паттерны по 4 проблемам графа понятий IWE.

## Изученные системы

| Категория | Система | Фокус изучения |
|-----------|---------|----------------|
| Академические онтологии | OBO Foundry | Quality gates, BFO-anchoring, editorial pipeline |
| LLM-era графы | Microsoft GraphRAG | Entity extraction, orphan detection, community detection |
| Образовательные графы | Knowledge Space Theory / ALEKS | Prerequisite-связи, surmise relation |
| Editorial + многоязычность | Wikidata | Label+alias система, property constraints |

## Паттерны

| Проблема | Система | Паттерн | Применимо к IWE | Как именно |
|----------|---------|---------|-----------------|------------|
| **orphan-prevention** | OBO Foundry | **BFO-anchoring:** каждый термин обязан быть потомком корневого узла верхнего уровня. Термин без `parent` к BFO = невалидный | Да | Каждое понятие обязано иметь ≥1 связь с одним из anchor-понятий (Система, Роль, Метод, Артефакт). Добавление без anchor-связи — статус `draft`, не `active` |
| **orphan-prevention** | OBO Foundry | **Definition gate:** термин без текстового определения = незавершённый. `definition` — обязательный атрибут при публикации | Да | Понятие без поля `definition` не переходит в статус `active`. 20 текущих orphan — скорее всего именно такие незавершённые записи |
| **orphan-prevention** | GraphRAG | **Entity resolution при добавлении:** при вставке нового узла LLM ищет семантически близкие существующие (same title+type → merge). Изолированный узел = сигнал, что entity не найдена ни в одном контексте | Частично | При добавлении понятия — автоматически предлагать похожие (fuzzy match по метке). Не найдено кандидатов → флаг «требует ревью редактора» |
| **orphan-prevention** | KST / ALEKS | **Surmise relation requirement:** каждый элемент знания обязан иметь хотя бы одну surmise-связь (prerequisite входящий или исходящий). Концепт без surmise relation физически не включается в knowledge space | Да | Для `guides` и `courses` — mandatory: хотя бы одна `prerequisite` связь. Понятие без prerequisite-in или prerequisite-out = orphan-кандидат, блокировать переход в `active` |
| **центральные узлы** | GraphRAG | **Community detection centrality:** Leiden-алгоритм выявляет «хабы». Если «Система» не является хабом — она семантически недосвязана, а не алгоритм ошибается | Да | Запустить degree centrality. Расхождение с ожидаемыми корневыми узлами = editorial задача: добавить bridge-связи от «Система» к зависящим понятиям |
| **центральные узлы** | OBO Foundry | **Mandatory upper-level bridging:** новые онтологии объявляют bridge axioms к BFO принудительно. Централизованный узел не возникает сам — его централизуют bridge-связями | Да | Каждое понятие из Pack декларирует принадлежность к anchor-понятию (`specializes` или `part_of`) как mandatory поле. Это механически повышает degree корневых узлов |
| **многоязычность** | Wikidata | **Label + Alias система:** каждый item имеет primary label на каждом языке + N aliases. Поиск по всем aliases всех языков. Constraint `label in language` проверяет наличие метки на обязательных языках | Да | 4 поля на понятие: `label_ru`, `label_en`, `aliases_ru[]`, `aliases_en[]`. Поиск — по всем четырём. Constraint при добавлении из Pack: оба языка обязательны; для `guides`/`courses` — хотя бы один |
| **editorial pipeline** | OBO Foundry | **Coordinators-as-editors:** новая онтология проходит peer review координаторов по чеклисту (scope, определения, уникальные ID, versioning, contact person). Без прохождения — статус `unreviewed`, не `active` | Да | Статусная модель: `draft → review → active`. Автоматические gate-проверки при переходе: definition, ≥1 связь, оба языка. Ручная проверка для `active` |

## Главные выводы для IWE

**1. Orphan — симптом незавершённости, не баг импорта.**
Все 4 системы предотвращают orphan-узлы через gate при добавлении, а не постфактум. Решение: блокировать переход `draft → active` без минимального набора (anchor-связь + definition).

**2. Центральность корневых узлов не возникает сама — её конструируют.**
GraphRAG и OBO Foundry используют принудительные bridge axioms. Для IWE: каждое новое понятие из Pack должно декларировать принадлежность к anchor-понятию (`Система`, `Роль`, `Метод`, `Артефакт`) как mandatory поле.

**3. Многоязычность: Wikidata-паттерн label+alias применим напрямую.**
4 поля (`label_ru`, `label_en`, `aliases_ru`, `aliases_en`), поиск по всем. Constraint «оба языка обязательны для Pack-понятий» снимает проблему без дополнительной логики.

**4. Editorial pipeline — это управление состоянием, не контроль качества.**
Статусная модель `draft/review/active` с автоматическими gate-проверками при переходе — единственный масштабируемый подход при 1126+ понятиях.

**5. KST-инсайт: prerequisite-связи — структурный инвариант, не украшение.**
В ALEKS понятие без surmise-связи физически не существует в learning space. Для IWE: понятия из `guides` и `courses` должны иметь prerequisite-структуру как обязательное условие — это одновременно orphan-prevention и педагогическое качество.

## Что НЕ применимо к IWE

| Паттерн | Почему не применимо |
|---------|---------------------|
| OBO Foundry peer review (внешние рецензенты) | IWE — авторский проект, нет внешних координаторов |
| GraphRAG полное LLM-извлечение из текста | Наш граф строится вручную из Pack-документов, а не из произвольного текста |
| ALEKS адаптивное тестирование знаний | Не образовательная платформа в чистом виде; тестирование — отдельная функция (WP-151) |
| Wikidata distributed editing (тысячи авторов) | Один-два автора графа; процесс упрощается |
