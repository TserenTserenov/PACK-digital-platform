---
id: DP.M.262
name: "Bidirectional cross-reference как защита от lifecycle coupling через чужой exec-механизм"
type: method
domain: digital-platform
pack_refs: []
trust: medium
epistemic_stage: empirical
status: active
valid_from: 2026-05-30
schema_version: 1
source: "peer-session 2026-05-30-27-wp-364-fork4-ale-calibration (03-peer.md)"
---

# DP.M.219 Bidirectional cross-reference для shared exec-mechanism

## Описание

При использовании чужого exec-mechanism (расширение чужого Telegram alert, чужого cron'а, чужого webhook'а) для **своего** lifecycle — возникает скрытое coupling: владелец чужого механизма меняет скрипт без знания о вашем потребителе → silent drift.

## IPO

**Вход:** ваш lifecycle (РП/фаза/процесс) триггерится через runtime concatenation в чужой exec-path (`MSG+=...`, `cron.append`, расширение чужого webhook handler'а).
**Процесс — bidirectional reference:**
1. **В источнике** — комментарий рядом с шарящейся строкой: `# WP-364:4b cross-reference — при изменении 4b меняй здесь`.
2. **В потребителе** — явный якорь: `exec-trigger: расширение Telegram-сообщения в scripts/check-wp353-trigger.sh:54`.
3. **Drift-detector** (optional) — линтер/тест ловит рассинхрон между двумя ссылками.

**Выход:** при изменении exec-mechanism владелец видит cross-reference → синхронизирует потребителя.

## Альтернатива (если bidirectional ref = overhead)

Собственный **independent TTL-reminder**: отдельный reminder-файл, свой cron, свой TTL. Два независимых напоминания, zero coupling. Подходит когда потребителей >1 или владелец exec-path не контролируется.

## Отличие от Pack-import

Pack-import: coupling декларативен (явный `pack_refs:` в frontmatter).
Здесь: coupling скрытый, через runtime concatenation — не виден из метаданных.

## Тест применимости

«Делаю ли я patch в чужой скрипт для своего lifecycle?» Да → обязательно bidirectional reference или вынести в independent reminder.

## Применимо к

- Shared cron-скрипты
- Telegram-alert hooks (расширение существующего MSG)
- Общие webhook handlers (вкручивание своего handler в чужой роутер)
- Любой magic-string injection в чужой exec-path