---
id: DP.FM.209
type: failure-mode
title: "SQL-инъекция через прямую подстановку CLI-параметра в строку запроса (f-string)"
pack: PACK-digital-platform
domain: platform-security
status: draft
valid_from: 2026-07-04
source: "session-close 2026-07-04 (WP-290, DS-autonomous-agents 2eee65e65)"
schema_version: 1
related:
  see_also: [DP.FM.083]
---

# DP.FM.209: SQL-инъекция через f-string/конкатенацию CLI-параметра в запрос

## Описание

Параметр из CLI-аргумента (дата, ID, строка) подставляется в SQL-запрос напрямую через f-string или конкатенацию. Позволяет произвольное исполнение SQL при подаче вредоносного значения.

## Класс дефекта

Уязвимость безопасности (OWASP Top 10 A03: Injection). Позволяет извлечь, изменить или удалить данные при контроле над параметром.

## Ситуация возникновения

```python
# Уязвимый код:
date = sys.argv[1]
cursor.execute(f"SELECT * FROM events WHERE date = '{date}'")
# Атака: date = "' OR '1'='1'; DROP TABLE events; --"
```

Также встречается в f-строках при конкатенации любого user-controlled значения: ID пользователя, имени таблицы, фильтра поиска.

## Фикс

Параметризованный запрос ($1 placeholder в psycopg2/asyncpg, ? в sqlite3):

```python
# Безопасно:
cursor.execute("SELECT * FROM events WHERE date = $1", (sys.argv[1],))
```

## Сопутствующий паттерн

Рассинхрон дефолта: код предполагал «пустой список» как дефолт, БД хранила NULL → нужен COALESCE. Оба паттерна часто появляются вместе при работе с необязательными CLI-аргументами.

## Тест

«Если передать `' OR '1'='1` вместо даты — что вернёт запрос?» Все строки → инъекция не закрыта.
