---
id: DP.FM.265
name: "Catch-all exception in fallback function masks systematic errors as normal behaviour"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-07
source: "WP-149 session-transcript 2026-07-07; _read_guide_file_safe() fix"
related:
  references: [DP.FM.169, DP.FM.167]
  see_also: ["DP.FM.169: тихий fallback в content pipeline", "DP.FM.167: silent False disables except fallback"]
tags: [fallback, exception-handling, http-errors, masking, catch-all]
---

# DP.FM.265 — Catch-All Exception Masks Systematic Errors

## Паттерн

Fallback-функция обещает возвращать нейтральное значение при ошибке (`""`, `None`, `0`).
Реализация использует `except Exception: return ""` — перехватывает ВСЕ исключения,
не различая тип отказа. Результат: ожидаемые ошибки (404 — файл не существует) и
неожиданные (500, 403, timeout, network error) обрабатываются идентично.
Систематические сбои скрываются под видом нормального поведения.

## Пример

```python
# Антипаттерн
def _yesterday_lever_title(self) -> str:
    try:
        return get_file_content(yesterday_path)
    except Exception:
        return ""   # 404 И 500 И timeout → одинаковый ""

# Fix: явное различение
def _read_guide_file_safe(path: str) -> str:
    try:
        return get_file_content(path)
    except HTTPError as e:
        if e.code == 404:
            return ""               # норма: файла не существует
        # retry один раз, потом raise
        time.sleep(2)
        try:
            return get_file_content(path)
        except Exception:
            raise GuideFetchError(f"Failed to read {path}: {e}")
    except (URLError, TimeoutError) as e:
        raise GuideFetchError(f"Network error reading {path}: {e}")
```

## Сигналы распознавания

- Функция возвращает нейтральное значение (`""`, `None`) при ошибке
- Реализация содержит `except Exception:` или `except:` без re-raise
- Docstring обещает «возвращает пустую строку при ошибке» без уточнения типа ошибки
- Ночной рендер/pipeline иногда молча «успешен» с пустым/деградировавшим output

## Отличие от DP.FM.169

| Аспект | DP.FM.169 | DP.FM.265 |
|--------|-----------|-----------|
| Источник проблемы | Тихая замена источника контента (wrong source) | Тихая обработка всех исключений без различения |
| Видимый симптом | Неверный контент, правильный файл | Правильная структура, скрытая сетевая проблема |
| Тест | «Контент соответствует ожидаемому источнику?» | «Тип исключения совпадает с обещанием функции?» |

## Тест

«Функция вернула `""`/`None` — какой тип ошибки произошёл?»
- Если нельзя ответить без логов → вероятно DP.FM.265.
- Тест-кейс: вызвать функцию с HTTPError(500). Если вернула `""` → дефект.
