---
id: DP.FM.018
name: Markdown Display-маркеры в data-полях (Markdown Markers in Data Fields)
category: data-pipeline
severity: minor
status: active
summary: "Поля Markdown-таблиц содержат display-разметку (**bold**, ~~strike~~), корректную для рендеринга, но ломающую downstream text-processing (sed, awk, jq, commit messages)."
created: 2026-05-11
valid_from: 2026-05-11
related:
  see_also: [DP.FM.007]
tags: [markdown, data-pipeline, shell, parsing, sed, display-vs-data, weekplan]
source: "git diff FMT-exocortex-template (commit db85a38, WP-5), 2026-05-11"
---

# [DP.FM.018] Markdown Display-маркеры в data-полях

## Суть паттерна

Поле Markdown-таблицы содержит display-разметку (`**жирный**`, `~~зачёркнутый~~`, `[ссылка](url)`), предназначенную для HTML-рендеринга. При чтении таблицы в shell-скрипт это поле используется как строка-данные. Downstream text-processing (sed, awk, jq, commit messages, TG-сообщения) получает артефакты разметки.

**Конкретный инцидент:** Поле `budget` в WeekPlan-таблице содержало `**bold**`-описание РП → сломался downstream-пайплайн, потребовался `sed 's/\*\*//g'`.

## Где проявляется

| Поле таблицы | Маркер | Downstream-проблема |
|---|---|---|
| Название РП | `**bold**` | Неверный commit message, неверная строка ChangeLog |
| Бюджет РП | `**bold**` | sed/awk-вычисление падает или возвращает 0 |
| Статус | `~~zerknuty~~` | Сравнение `~~done~~` ≠ `done` — условие не срабатывает |
| Ссылка | `[text](url)` | TG-сообщение показывает `[text](url)` вместо `text` |

## Правило предотвращения

**При считывании Markdown-таблиц в shell-скрипты — strip display-маркеров на входе для всех полей, используемых как data.**

```bash
strip_markdown() {
    local value="$1"
    value="${value//\*\*/}"          # **bold**
    value="${value//\~\~/}"          # ~~strike~~
    value=$(echo "$value" | sed 's/\[[^]]*\]([^)]*)/<a>/g')  # [text](url)
    echo "$value"
}
```

Применять ко всем полям pipeline (название, бюджет, статус), не только к тем, что уже сломались.
