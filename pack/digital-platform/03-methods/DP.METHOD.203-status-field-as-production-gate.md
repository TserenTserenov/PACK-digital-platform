---
id: DP.METHOD.203
type: method
domain: PACK-digital-platform
status: draft
summary: "Поле status в трекере backlog'а как формальный production gate: только записи со status=accepted попадают в pipeline исполнения. Rejected-записи не удаляются — формируют anti-pattern corpus. Применимо к любому конвейеру с регулярными артефактами."
created: 2026-07-18
valid_from: 2026-06-27
version: v1.0
source: "git diff DS-Tseren-Brand (commit d5a6524, WP-442 Ф10); extraction-report 2026-06-27-inbox-check-4 #4"
related:
  see_also: [DP.METHOD.064, DP.METHOD.206]
tags: [content-pipeline, backlog, production-gate, status-field, anti-pattern-corpus]
---

# DP.METHOD.203: Поле status как production gate в трекере backlog'а

## Контекст

В конвейерах с регулярными публикациями (контент, задачи, отчёты) идеи/темы/задачи накапливаются в backlog'е. Без формального gate всё накопленное считается «кандидатом» — нет чёткой границы что идёт в исполнение, а что нет.

## Правило

1. **Трекер-файл** (YAML, markdown-таблица) хранит все записи включая rejected — удалять нельзя.
2. **Поле `status`** — единственный gate: только `accepted` → pipeline исполнения (DayPlan, задача, публикация). `proposed` и `rejected` — в трекере, но не исполняются.
3. **Rejected corpus** — rejected-записи с пометкой причины формируют anti-pattern corpus: «что не работает для этого контекста». Ценен для будущих решений.
4. **Acceptance явный:** `proposed → accepted` = осознанное решение, не автоматический перевод.

## Структура записи

```yaml
- id: C-001
  date: 2026-06-26
  type: T1            # формат артефакта
  title: "..."
  area: "..."         # домен
  status: proposed    # proposed | accepted | rejected | published
  # draft_path: null  # заполняется при создании файла, не при accepted
```

## Применимость

- Контент-конвейер с регулярными публикациями
- Backlog задач с явным entry-gate
- Любая система, где нужно различать «намерение принято» vs «артефакт существует»

## Тест

«При смене автора (или через месяц) видно, почему конкретная запись не пошла в исполнение?» Нет → rejected-причина не зафиксирована (антипаттерн).

## Различение

**acceptance (status=accepted) ≠ artifact-exists (draft_path != null):** принятие намерения и наличие артефакта — два разных события (см. DP.METHOD.206 — nullable draft_path).

## Связи

- see_also: DP.METHOD.064 (gate по outcome-pending — про РП-фазы, семантически смежное)
