---
id: DP.METHOD.206
name: Nullable draft_path как разделитель Intent-Accepted vs Artifact-Exists
type: method
status: draft
domain: content-lifecycle-tracking
created: 2026-07-18
valid_from: 2026-06-27
tags: [content-pipeline, lifecycle, tracking, nullable-field, intent-vs-artifact]
source: "session-transcript 2026-06-26 WP-442 Ф9 + commit 117ecd3; extraction-report 2026-06-27-inbox-check-5 #3"
schema_version: 1
related:
  see_also: [DP.METHOD.064, DP.METHOD.203]
---

# [DP.METHOD.206] Nullable draft_path как разделитель Intent-Accepted vs Artifact-Exists

## Суть метода

Использовать nullable поле `draft_path` (null при принятии намерения, заполняется путём при создании файла) вместо boolean `draft: true/false` для трекинга состояния контентного элемента в pipeline.

## Проблема (которую решает метод)

Boolean `draft: true` не различает два разных состояния:
- **Intent-accepted:** идея принята, файл ещё не создан.
- **Artifact-exists:** файл создан, но не опубликован.

Следствие: «draft: true» читается как «готово к работе», хотя файла нет → false-positive в pipeline.

## Решение

```yaml
# При принятии темы (acceptance):
accepted_date: "2026-06-26"
draft_path: null          # намерение принято, артефакта нет

# При создании производственного файла:
draft_path: "drafts/2026/topic-slug.md"   # артефакт существует
```

## IPO

**Вход:** трекер контентных тем/задач с полем статуса.

**Процесс:**
1. При acceptance — ставить `accepted_date`, `draft_path: null`.
2. При создании производственного файла — заполнять `draft_path` реальным путём.
3. Pipeline читает: `draft_path is not null` → файл существует; `draft_path is null` → принято, не начато.

**Выход:** чёткое разделение «принято» / «существует» без boolean-двусмысленности. Видно «принято, но не начато» без false-positive.

## Применимость

Любой трекер задач/контента/РП, где нужно различать intent-accepted vs artifact-exists — два разных поля/состояния, не один boolean.

## Связи

- Уточняет DP.METHOD.064 (outcome-gate-pending-status) — там про pending-статус РП, здесь про nullable-поле для артефакта.
- see_also: DP.METHOD.203 (status как production gate — родительский паттерн трекера).
