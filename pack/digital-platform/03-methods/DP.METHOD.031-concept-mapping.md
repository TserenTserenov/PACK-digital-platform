---
id: DP.METHOD.031
name: Метод онтологического сопоставления Pack-понятий с FPF-корнями
type: method
status: active
valid_from: 2026-04-15
summary: "Алгоритм назначения FPF-корня (U.*) для нового Pack-понятия. Предотвращает изолированные понятия и silent drop рёбер при индексации."
related:
  uses:
    - DP.SOTA.018
    - DP.SC.029
    - DP.SOTA.017
  informs:
    - DP.METHOD.030
created: 2026-04-15
updated: 2026-04-15
---

# DP.METHOD.031 — Метод онтологического сопоставления Pack-понятий с FPF-корнями

> **see DP.SC.029, DP.SOTA.018**
> Применяется: при создании нового Pack-документа, при добавлении нового prefix-пространства, при аудите orphan-понятий.

## §1 Принципы

1. **Каждое Pack-понятие имеет ≥1 ребро к FPF-корню (U.*)** — инвариант графа.
2. **Маппинг prefix → U.\* зафиксирован в `SPF_TYPE_TO_FPF`** в knowledge-mcp. Новый prefix = новая строка в маппинге ДО первой индексации.
3. **Fallback = U.Episteme** — если FPF-корень неочевиден, использовать U.Episteme и создать issue для уточнения.
4. **Слабая связь допустима**: если понятие лишь косвенно относится к U.* — использовать `related →` вместо `specializes →`.

## §2 Таблица сопоставления (prefix → FPF-корень)

| Prefix | FPF-корень | Тип ребра | Обоснование |
|--------|-----------|-----------|-------------|
| `DP.ARCH.*` | U.System | specializes | Архитектурные решения описывают системы |
| `DP.ROLE.*` | U.RoleAssignment | specializes | Роли = назначения на функцию |
| `DP.METHOD.*` | U.Method | specializes | Методы = воспроизводимые способы достижения |
| `DP.SC.*` | U.ServiceClause | specializes | Обещания = сервисные клаузы |
| `DP.SOTA.*` | U.Episteme | specializes | SoTA = знание о состоянии отрасли |
| `DP.D.*` | U.Episteme | specializes | Различения = знание |
| `DP.ECON.*` | U.ServiceClause | specializes | Экономика = обещания ценности |
| `DP.IWE.*` | U.System | specializes | IWE-конструкты = системы |
| `DP.FM.*` | U.Work | related | Failure modes = паттерны неудачной работы |
| `PD.*` | U.Work | specializes | Паттерны деятельности |
| `MIM.*` | U.RoleAssignment | specializes | Методологические роли МИМ |
| `DP.CONCEPT.*` | U.Episteme | specializes | Концепты = знание |
| `DP.WP.*` | U.Work | specializes | Рабочие продукты = результаты работы |

> **Новый prefix:** добавить строку в эту таблицу И в `SPF_TYPE_TO_FPF` в `knowledge-mcp/src/ingest-concepts.ts` одновременно.

## §3 Алгоритм сопоставления

```
Шаг 1. Определить prefix документа (DP.ARCH, DP.D, MIM, PD, …)

Шаг 2. Найти в таблице §2:
  → Нашёл: использовать указанный U.* и тип ребра → Шаг 4
  → Не нашёл (новый prefix): перейти к Шагу 3

Шаг 3. Новый prefix — определить FPF-корень вручную:
  Вопрос 1: «Это система/архитектура?» → U.System
  Вопрос 2: «Это роль/назначение?» → U.RoleAssignment
  Вопрос 3: «Это метод/способ?» → U.Method
  Вопрос 4: «Это обещание/сервис?» → U.ServiceClause
  Вопрос 5: «Это знание/различение?» → U.Episteme
  Вопрос 6: «Это деятельность/продукт работы?» → U.Work
  Неоднозначно → U.Episteme (fallback) + issue в fleeting-notes.md [ontology-mapping]
  Добавить строку в таблицу §2 + строку в SPF_TYPE_TO_FPF

Шаг 4. Добавить в frontmatter документа:
  related:
    specializes: [U.<Корень>]   # или related: [U.<Корень>] для слабой связи

Шаг 5. Верификация при следующей индексации:
  knowledge_graph_stats → degree U.<Корень> увеличился на ≥1
```

## §4 Примеры правильного и неправильного сопоставления

| Понятие | Правильно | Неправильно | Почему |
|---------|-----------|-------------|--------|
| DP.ROLE.012 Стратег | `specializes → U.RoleAssignment` | `specializes → U.Method` | Стратег — это назначение, не метод |
| DP.METHOD.030 Метод перевода | `specializes → U.Method` | `specializes → U.Episteme` | Метод — это способ действия, не знание |
| DP.FM.010 Прыжок в реализацию | `related → U.Work` | `specializes → U.Work` | Failure mode косвенно связан с работой |
| DP.D.046 Экзоскелет | `specializes → U.Episteme` | `specializes → U.System` | Различение — это знание, не система |

## §5 Правила для агента

```
Для каждого Pack-документа:
1. Прочитай id и определи prefix
2. Найди FPF-корень в таблице (§2)
3. Если prefix новый — ответь на 6 вопросов из §3
4. Добавь в related.specializes или related (для слабой связи)
5. Если неоднозначно — поставь U.Episteme + отметь [ontology-mapping] в комментарии
Формат ответа: | id | prefix | U.* | тип ребра | обоснование |
```

## §6 Критерий готовности

- Frontmatter содержит `related.specializes: [U.<Корень>]` или `related: [U.<Корень>]`
- Prefix зарегистрирован в `SPF_TYPE_TO_FPF` (для нового prefix)
- При следующей индексации: degree U.* увеличился, понятие не в списке orphans
- pack-lint.sh выдаёт «OK» по R1 (наличие related к U.*)
