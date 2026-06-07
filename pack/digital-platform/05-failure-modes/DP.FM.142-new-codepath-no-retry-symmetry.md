---
id: DP.FM.142
name: New codepath no retry-symmetry — новый code-path без retry-симметрии с legacy-path
type: fm
domain: digital-platform
status: active
valid_from: 2026-06-06
source: DS-my-strategy session-close 2026-06-06, WP-330 marathon hotfix, git diff DS-IT-systems
---

# DP.FM.142 — New codepath no retry-symmetry

## Симптом

В боевом журнале видны записи с `status=failed` и `attempts=1`. Первая transient-ошибка помечает задачу как failed без шанса на retry.

## Механизм

При добавлении нового code-path (новый формат контента, новый тип задачи в scheduler) разработчик реализует happy path, но не копирует retry-логику из legacy-пути.

- **Legacy-путь:** `if attempts < 2 → retry with backoff 30→60 мин`
- **Новый путь:** `mark_queue_failed()` на первой ошибке без проверки `attempts`

Результат: транзиентная ошибка (asyncpg timeout, network blip) даёт permanent failure.

## Диагностика

Найти `failed`-записи с `attempts=1` в очереди. Если их доля > 0 — вероятно, retry-логика не симметрична.

## Фикс

Аудит всех exit-point нового пути → зеркальный перенос retry/backoff из соответствующего legacy-пути.

## Тест

«Есть ли у нового code-path такая же retry-логика, как у старого?» Нет → паттерн нарушен.

## Класс ошибки

Silent failure (система считает задачу completed/failed, хотя это retryable error).
