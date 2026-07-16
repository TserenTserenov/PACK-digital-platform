---
id: DP.FM.300
name: "Исключение кода retry-механизма поглощается внешним try/except"
type: failure-mode
status: active
valid_from: 2026-07-10
source: "WP-149 ФК1; session-transcript 2026-07-10-20; DS-autonomous-agents commit 7e6bcd2"
related:
  see_also: [DP.FM.265, DP.FM.093]
tags: [retry, exception-handling, alert, silent-failure, two-level-try]
---

# DP.FM.300 — Исключение кода retry-механизма поглощается внешним try/except

## Симптом

Оператор видит «молчание» после N неудачных попыток — нет алерта «retry сломана», нет диагностики. Выглядит как «все попытки исчерпаны», но реальная причина: код генерации retry-запроса сам упал.

## Механизм

```python
# Антипаттерн: один try-уровень для двух разных причин провала
def with_retry(fn, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return fn()
        except Exception:
            new_request = generate_retry_request()  # может упасть само
            # Если generate_retry_request() кинул исключение — оно здесь же
            # Счётчик идёт вперёд, «retry сломана» незаметно
    raise MaxRetriesExceeded()
```

Когда `generate_retry_request()` бросает исключение, оно перехватывается тем же `except Exception`, что и провалы основной функции. Оператор не различает «функция провалилась» vs «retry-код провалился».

## Отличие от DP.FM.265

| Аспект | DP.FM.265 | DP.FM.300 |
|--------|-----------|-----------|
| Где происходит | Fallback-функция (`except: return ""`) | Retry-механизм (генерация нового запроса) |
| Симптом | Тихое нейтральное значение | Исчерпаны попытки без диагноза «retry сломана» |
| Fix | Различить тип исключения в fallback | Двухуровневый try с distinct alert |

## Фикс

```python
def with_retry(fn, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return fn()
        except RetryableError:
            try:
                new_request = generate_retry_request()
            except Exception as e:
                send_alert(f"Retry mechanism itself failed: {e}")
                raise  # не засчитывать как «ещё одна попытка»
    raise MaxRetriesExceeded()
```

## Тест

«Код генерации retry упал — что получает оператор?» Молчание/MaxRetriesExceeded → вероятно DP.FM.300. Distinct alert «retry сломана» → фикс применён.
