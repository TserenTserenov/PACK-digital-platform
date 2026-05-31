---
id: DP.M.181
title: "Паттерн двух файлов для multi-turn агентной сессии"
type: method
status: draft
domain: digital-platform
valid_from: 2026-05-27
source: WP-358, session-transcript 2026-05-27
---

# DP.M.181 — Multi-turn Session Thread Pattern

## Суть

Для агентной сессии с несколькими ходами используются два файла:
- `SESSION-<id>.md` — метаданные сессии (session_id, status, timestamps)
- `SESSION-<id>-thread.md` — живой журнал ходов (дописывается при каждом новом ходе)

Идентификатор хода: `(session_id, turn_n)`.
Идентификация активной сессии: composite key `(tg_chat_id, active_session_id)` из SQLite.

## Инвариант

Metadata-файл не растёт при добавлении ходов — thread-файл дописывается.
Агент стартует новый ход с полным контекстом: метаданные + все предыдущие ходы из thread-файла.

## Применимость

Любой async multi-turn агентный интерфейс (Telegram, email, webhook).

## Связи

- SC: DP.SC.162 (External Session Request)
- Реализация: WP-358
