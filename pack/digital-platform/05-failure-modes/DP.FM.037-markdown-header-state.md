---
id: DP.FM.037
name: Парсинг состояния по заголовку шаблона vs значению из frontmatter (Markdown Header Presence vs Frontmatter Value State Detection)
category: data-pipeline
severity: medium
status: active
summary: "Детектор состояния использует `grep` по заголовку секции (`### 🔴 Critical`), который присутствует в шаблоне всегда — false-positive при пустой секции. Состояние должно парситься из значения в YAML frontmatter, а не из наличия заголовка."
created: 2026-05-15
valid_from: 2026-05-15
related:
  see_also: [DP.FM.015, DP.FM.018]
tags: [markdown, parsing, alerting, false-positive, frontmatter, state-detection, monitoring]
source: "git diff DS-autonomous-agents (2d84a4e — fix(auditor): notify_if_critical scripts/overnight-auditor.sh), 2026-05-15"
schema_version: 1
---

# [DP.FM.037] Парсинг состояния по заголовку шаблона vs значению из frontmatter

## Суть паттерна

Детектор состояния markdown-отчёта использует `grep -c '^### 🔴 Critical'` для проверки «есть ли критические флаги». Заголовок секции — часть **структуры шаблона**: он присутствует ВСЕГДА, даже когда секция пустая (счётчик = 0).

Результат: детектор всегда возвращает ≥1 → false-positive алёрт при каждом запуске.

**Корневое смешение:** структура (стабильна) ≠ факт (меняется). Парсинг наличия заголовка отвечает на вопрос «генерирует ли шаблон эту секцию», а не «есть ли в ней содержимое».

## Где проявляется

| Контекст | Симптом |
|---|---|
| Daily auditor reports → TG-алерт | Ежедневные «у вас критические флаги» при `flags_count.critical = 0` |
| Health checks по dashboard'ам | Always-red статус при шаблоне с заголовками всех состояний |
| Threshold-алерты по log-файлам | False-trigger при presence-of-pattern, а не at-volume |
| Любой markdown-отчёт с фиксированной структурой секций | Везде, где «секция X» = часть шаблона |

## Корень

```bash
# Антипаттерн: presence-of-template-element как факт
critical_count=$(grep -c '^### 🔴 Critical' report.md)
[ "$critical_count" -gt 0 ] && notify "critical!"
# critical_count всегда = 1 (заголовок есть в шаблоне)
```

vs

```bash
# Правильно: значение из frontmatter (генерируется на основе данных)
critical_count=$(yq '.flags_count.critical' report.md)
[ "$critical_count" -gt 0 ] && notify "critical!"
# critical_count меняется в зависимости от факта
```

## Решение

**Правило проектирования markdown-отчётов с автоматическим состоянием:**

1. **Все состояния-данные → YAML frontmatter** (`flags_count.critical: 3`, `status: red`, `open_issues: 12`).
2. **Заголовки секций — только для людей** (структура шаблона, не источник факта).
3. **Детекторы парсят frontmatter** (`yq`, `python -c "import yaml"`, `awk` по `key: value`), не grep по `^### `.

**Пример frontmatter-схемы:**

```yaml
---
type: audit-report
date: 2026-05-15
flags_count:
  critical: 0
  warning: 2
  info: 5
status: green
---
```

## Профилактика

- **Smoke-test детектора на пустом отчёте:** новый markdown-генератор → запустить детектор на «всё ноль» → ожидание = тишина, не алёрт
- **Convention в шаблонах отчётов:** машино-читаемая часть → frontmatter, человеко-читаемая → body
- **Code review:** grep по проектным алёртам на `grep -c '^### '` или `grep -c '^## '` — кандидаты на пересмотр

## Прецеденты

- **WP-315 / overnight-auditor (2026-05-15):** `scripts/overnight-auditor.sh` парсил `^### 🔴 Critical` для notify_if_critical → ежедневные false-positive TG-алерты, поскольку секция в шаблоне всегда. Fix `2d84a4e`: переход на `flags_count.critical:` из frontmatter (как в notification template).

## Связи

- **DP.FM.015** False-Positive Capture Detection — родственный markdown-parsing FM (parent vs subsection); здесь — структура vs значение
- **DP.FM.018** Markdown Display-маркеры в data-полях — другая ось разделения display vs data
- **DP.D.049** Лог ≠ Инцидент ≠ State file — фактическое состояние имеет конкретное место хранения, не «следы в шаблоне»
