---
id: DP.FM.289
name: "SQLite date() возвращает NULL: offset без двоеточия (+HHMM) не распознаётся"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-14
source: "session-close 2026-07-09; DS-my-strategy commit c254cec (extensions/day-open.summary-extra.sh, WP-470)"
related:
  see_also:
    - "DP.FM.040: silent-null-parser (мета-паттерн: парсер молча возвращает null)"
    - "DP.FM.221: timezone-msk-utc-date-comparison (смежно: timezone в date-сравнении)"
tags: [sqlite, date, timezone, null, apple-health, iso8601, false-empty, substr]
---

# DP.FM.289 — SQLite date() возвращает NULL: нестандартный offset +HHMM не распознаётся

## Паттерн

SQLite функции `date()`, `strftime()`, `datetime()` парсят timezone offset только в формате
`+HH:MM` (RFC 3339 / ISO 8601 extended). Если источник использует `+HHMM` без двоеточия
(например Apple Health), функции возвращают NULL **молча**, без ошибки.
Запрос `WHERE date(date_col) = '2026-07-09'` возвращает 0 строк при наличии данных.

## Пример

```sql
-- Apple Health формат: "2026-07-09 07:30:00 +0300" (нет двоеточия)

-- СЛОМАНО — date() не может распарсить +0300 → NULL:
SELECT * FROM health WHERE date(received_at) = '2026-07-09';
-- Возвращает 0 строк, хотя данные за 2026-07-09 есть.

-- Диагностика: проверить что date() возвращает
SELECT DISTINCT date(received_at) FROM health LIMIT 5;
-- Результат: только NULL → формат не распознан

-- ПРАВИЛЬНО — substr по первым 10 символам:
SELECT * FROM health WHERE substr(received_at, 1, 10) = '2026-07-09';
-- Возвращает правильные строки.
```

## Симптом

`date(col) = 'YYYY-MM-DD'` или `strftime('%Y-%m-%d', col) = 'YYYY-MM-DD'` возвращает
0 строк несмотря на наличие данных за эту дату.

## Диагностика

```sql
-- Шаг 1: проверить, что date() разбирает хоть что-то
SELECT DISTINCT date(date_col) FROM table LIMIT 5;
-- Только NULL → формат не распознан

-- Шаг 2: убедиться, что строки физически есть
SELECT COUNT(*), substr(date_col, 1, 10) FROM table GROUP BY 2 LIMIT 10;
```

## Fix

Заменить `date(col) = 'YYYY-MM-DD'` на `substr(col, 1, 10) = 'YYYY-MM-DD'`.
Предполагает, что дата всегда начинается с первых 10 символов в формате ISO 8601.

## Применимость

Apple Health экспорт (export.xml → SQLite): формат `"YYYY-MM-DD HH:MM:SS +HHMM"`.
Любой источник данных с ISO 8601 offset без двоеточия.
SQLite до версии 3.43 (когда +HHMM может не поддерживаться).

## Связи

- **DP.FM.040** (Silent-Null Parser) — мета-паттерн: парсер молча возвращает null на нераспознанный формат. Этот FM — конкретный инстанс для SQLite date functions.
- **DP.FM.221** (timezone-msk-utc-date-comparison) — смежно: проблемы с timezone в date-запросах SQLite; FM.221 = неправильный часовой пояс, этот = NULL из-за формата offset.
