---
id: DP.M.172
type: method
title: Вариант B архивации knowledge-файлов — git mv в archive/ вместо удаления
status: active
source: git diff DS-my-strategy (f04be4c6 WP-316 Ф11) — 99 feedback_*.md архивированы
valid_from: 2026-05-25
related:
  uses: [AS.D.018]
---

# DP.M.172 — Knowledge-file archive: Variant B (git mv)

## Проблема

При накоплении N knowledge-файлов (feedback_*.md, lessons_*.md) в активной директории:
- **Вариант A (удаление):** теряется исследовательская ценность и git-история.
- **Вариант B (git mv в archive/):** файлы доступны через `git log -p`, но не попадают в context scan агента.

## Решение: Variant B

```bash
git mv memory/feedback_*.md memory/archive/feedback/
git mv memory/lessons_*.md memory/archive/lessons/
git commit -m "archive: move N knowledge files to reduce context pollution"
```

**Принцип разделения:**
- **Агент использует систематически** → SQLite (primary, queryable, decaying)
- **Человеку нужно для ретроспективы** → archive/ (git-accessible, не в hot-directory)

## Когда применять

| Сигнал | Действие |
|--------|---------|
| N > 30 файлов в одной директории | Предложить архивацию на Week Close |
| Файл не открывался > 30 дней | Кандидат на архивацию |
| Файл дублирован в SQLite | Приоритетный кандидат |

## Тест

«Нужен ли файл агенту в текущей сессии?»
- Нет → archive/, не hot-directory.
- Да, систематически → SQLite.

## Связи

- AS.D.018 — Hermes (SQLite = primary после архивации файлов)
