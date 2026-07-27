---
id: DP.FM.136
type: failure-mode
title: Неякорный grep для frontmatter-поля — false-positive из body-текста
trust: observed
epistemic_stage: confirmed
domains: [ci-checks, frontmatter-validation, bash-scripting]
source_session: 2026-06-06 session-close (FMT-exocortex-template extensions/protocol-close.checks.md)
source_commit: "20f0afa (fix(checks): anchored grep ^status:)"
valid_from: 2026-06-06
schema_version: 1
---

# DP.FM.136 — Неякорный grep для frontmatter-поля → false-positive

## Симптом

Скрипт проверяет наличие YAML-поля в frontmatter через `grep "status:"`. Строка `status:` присутствует и в body-тексте документа (логи, отчёты, статус-блоки) → grep находит совпадение → файл без нужного frontmatter-поля проходит проверку.

Файл считается валидным, хотя frontmatter-поле отсутствует.

## Корень

`grep "status:"` совпадает с любым вхождением подстроки в файле. YAML-frontmatter занимает первые строки, но без ограничителя grep ищет по всему содержимому.

## Профилактика

Использовать якорный grep: `grep "^status:"` — совпадение только с началом строки.

Применимо к любым YAML-полям в frontmatter markdown-документов:

```bash
# Неправильно — может сработать на body-текст
grep "status:" "$file"

# Правильно — только frontmatter-строка
grep "^status:" "$file"
```

Паттерн: `^date:`, `^type:`, `^horizon:`, `^priority:` — все поля frontmatter требуют якоря.

## Применимо к

- CI-скрипты валидации frontmatter
- Pre-commit хуки с проверкой обязательных полей
- Extraction-report парсеры (поиск `status: pending-review` в теле ≠ frontmatter)

## Связано

- FMT-exocortex-template/extensions/protocol-close.checks.md — источник fix
