---
id: DP.M.384
name: "Close Known Gap Immediately During Review (Немедленное закрытие known-gap при ревью)"
type: method
domain: digital-platform / review-patterns / known-gap-handling
status: active
valid_from: 2026-07-13
sources:
  - session-transcript 2026-07-13, WP-450 hot-context-slim (git diff a4d58a4d5)
related:
  see_also: [DP.FM.286]
tags: [review, known-gap, protocol-files, throughput, defer-antipattern, hot-file-review]
---

# DP.M.384 — Немедленное закрытие known-gap при ревью

## Суть метода

При ревью протокольных файлов (CLAUDE.md, distinctions.md, memory/*.md) и обнаружении known gap, устранимого за <15 мин: **закрыть немедленно**, не открывать отдельный РП.

## Проблема (антипаттерн)

«Задокументирую known gap → открою РП → закрою в следующую сессию» — gap остаётся открытым неделями. Документирование без действия = Inventory (запасы), не Throughput (пропускная способность).

## Алгоритм

1. При ревью hot-файла обнаружен gap.
2. Оценить: устранимо за <15 мин? Да → закрыть здесь и сейчас.
3. 1 коммит, тема: `fix: <что закрыто> (<откуда взят gap, например WP-450 Ф6>)`.
4. Нет — задача нетривиальна: WP Gate (Правило 1 CLAUDE.md) → РП с явной формулировкой.

## Граница применения

- **<15 мин:** немедленный фикс.
- **>15 мин и новый артефакт:** WP Gate обязателен — P.384 не отменяет Правило 1.

## Пример

WP-450 ревью CLAUDE.md: обнаружен known gap Ф6 — calendar event для month-close не существовал (упомянут в WP-450 как пропуск). Вместо нового РП → `feat(calendar): add month-close reminder`, 1 коммит, <15 мин. Gap закрыт в той же сессии.

## Тест

«Если закрыть сейчас — нужно ли новое артефактное пространство?» Нет + <15 мин → закрывать немедленно.

## Связи

- **Правило 7 CLAUDE.md** (Автономность) — основание: задача <15 мин без нового артефакта = тривиальная, WP Gate не нужен. DP.M.384 — explicit формулировка этого правила для контекста ревью hot-файлов.
- **DP.FM.286** (Silent Semantic Loss on Hot-File Compression) — known gap при ревью может быть следствием FM.286.
