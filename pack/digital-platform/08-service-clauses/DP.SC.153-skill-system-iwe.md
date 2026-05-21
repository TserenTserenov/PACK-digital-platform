---
id: DP.SC.153
name: Скилл-система IWE
name_en: IWE Skill System
type: sc
status: draft
layer: L4-Platform
summary: "Разработчик IWE получает: каталог всех скиллов с метаданными и графом зависимостей; конвейер создания (create-skill.sh → validate → promote); безопасное обновление через versioning без перезаписи L3-кастомизаций."
consumer: Разработчик IWE (DP.ROLE.001), Мейнтейнер скиллов (DP.ROLE.056), Автор скилла (DP.ROLE.057)
created: 2026-05-21
updated: 2026-05-21
related:
  specializes: []
  realizes: []
  uses:
    - DP.ROLE.056               # Мейнтейнер скиллов
    - DP.ROLE.057               # Автор скилла
    - .claude/skills/           # каталог скиллов (артефакт системы)
  see_also:
    - DP.SC.036                 # Routing Gate (связанный паттерн маршрутизации)
wp: WP-348
---

# [DP.SC.153] Скилл-система IWE

## Правило (инвариант)

> Нарушение любого = провал SC.

- **Единый источник истины — `skills-catalog.yaml`.** Каталог генерируется автоматически из SKILL.md всех скиллов. Ручное редактирование каталога запрещено.
- **SKILL.md v2 обязателен для promote.** Скилл без обязательных полей (`name`, `description`, `version`, `layer`, `status`, `triggers`) не может быть промотирован из L3 в L1.
- **`invoked_by` — вычисляемое поле.** Не хранится в frontmatter SKILL.md. Всегда берётся из `skills-catalog.yaml`.
- **L3-кастомизации защищены.** При `iwe-update` / `template-sync` платформенные (L1) скиллы не перезаписывают авторские (L3) без явного подтверждения.
- **DP.SC обязателен перед promote.** Новый L1-скилл должен иметь ссылку на свой Service Clause (или использовать DP.SC.153 как родительский).
- **Зависимости декларируются явно.** `depends_on` в frontmatter — единственный источник для построения графа. Текстовые упоминания в теле SKILL.md не считаются.

---

## Обещание

**Кому:**
- **Разработчик IWE (DP.ROLE.001)** — «какие скиллы есть, кто их вызывает, как создать новый»
- **Мейнтейнер скиллов (DP.ROLE.056)** — «каталог актуален, promote работает, граф зависимостей строится»
- **Автор скилла (DP.ROLE.057)** — «есть стандарт, есть инструмент, есть путь в платформу»

**Зачем:**
- 35+ скиллов без каталога → невозможно обнаружить дублирование, устаревшие скиллы, нарушенные зависимости
- Стихийное создание скиллов → несовместимость между репозиториями, fragile promote pipeline
- Отсутствие layer encoding → `iwe-update` может перезаписать авторские скиллы

**Что получит:**

```
{
  "catalog": "skills-catalog.yaml с N скиллами",
  "dependency_graph": "invoked_by для каждого скилла (вычислен)",
  "validation": "promote блокирован если SKILL.md v2 не пройдён",
  "safe_update": "L3 скиллы не перезаписаны при iwe-update"
}
```

**Время отклика:**
- `generate-skills-catalog.sh` — < 5 сек для 50 скиллов
- `validate-skill.sh` — < 2 сек на скилл
- `create-skill.sh` — < 3 сек (создание структуры)

---

## Конвейер (основной flow)

```
Автор → create-skill.sh → SKILL.md v2 scaffold
                        → validate-skill.sh (frontmatter + depends_on)
                        → smoke-test (ручной)
                        → skill-promote.sh → L1 (FMT)
                        → generate-skills-catalog.sh → skills-catalog.yaml обновлён
```

**Pull pipeline (L1→L3):**
```
iwe-update → template-sync.sh → merge L1 skills into ~/.claude/skills/
           → L3 скиллы (layer: L3) → НЕ перезаписываются
           → L1 скиллы (layer: L1) → перезаписываются только если нет локальных изменений
```

---

## Режим отказа

| Сценарий | Поведение |
|---------|-----------|
| SKILL.md v2 validation провален | `validate-skill.sh` exit 1, сообщение о пропущенных полях |
| Дублирующийся `name` в `create-skill.sh` | exit 1, предложить другое имя |
| `depends_on` ссылается на несуществующий скилл | `validate-skill.sh` предупреждение (не блокер) |
| `iwe-update` обнаруживает конфликт L1 vs L3 | pause + report пользователю, не автоматическое разрешение |
| `generate-skills-catalog.sh` не может распарсить SKILL.md | пропустить этот скилл с предупреждением, не прерывать |
