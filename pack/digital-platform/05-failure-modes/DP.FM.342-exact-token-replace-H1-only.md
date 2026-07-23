---
id: DP.FM.342
name: "Exact-token replace без ограничения строкой H1 даёт ложные срабатывания при выравнивании ID"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-23
created: 2026-07-23
source: "WP-170 batch 4, capture #57"
tags: [id-alignment, exact-token-replace, false-positive, H1]
---

# DP.FM.342 — Exact-token replace без ограничения строкой H1 даёт ложные срабатывания

## Паттерн

Автоматическое выравнивание ID (frontmatter ↔ заголовок H1) через replace по точному токену — если replace не ограничен строкой H1, тот же токен может встретиться в теле документа (пример, ссылка, цитата) и быть заменён там, где замена не нужна или искажает смысл.

## Профилактика

Скрипт выравнивания ID через exact-token replace → ограничивать замену конкретно строкой H1 (первая строка `# ID: ...`), не применять replace ко всему файлу.
