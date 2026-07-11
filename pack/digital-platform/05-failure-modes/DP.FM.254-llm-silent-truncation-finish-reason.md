---
id: DP.FM.254
name: "LLM silent truncation: finish_reason=length без проверки записывает частичный вывод"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-07-10
source: "captures/2026-07-06 feed:session-close; FMT-exocortex-template translate.py commit 8562439"
related:
  references: [DP.FM.243]
  see_also: ["DP.FM.243 — детерминированный расчёт LLM"]
tags: [llm, truncation, finish-reason, silent-failure, pipeline]
---

# DP.FM.254 — LLM silent truncation: finish_reason=length без проверки записывает частичный вывод

## Паттерн

LLM-пайплайн выставляет произвольный `max_tokens`, получает `finish_reason=length` (модель остановилась не по EOS, а по лимиту), но не проверяет это поле и записывает частичный вывод как полный.

## Пример

```python
# translate.py — произвольный cap max_tokens=8192
# docs/LEARNING-PATH.md (~1700 строк) молча переведён наполовину
# файл записан, ошибка не поднята

# Фикс:
if response.choices[0].finish_reason == "length":
    raise TranslationTruncated(f"Output truncated at {max_tokens} tokens")
```

## Механизм

Модель возвращает HTTP 200 со статусом `finish_reason=length` вместо `stop`. Без явной проверки код воспринимает ответ как успешный и записывает частичный контент. Видимый эффект: файл обрезан на полуслове, но «выглядит переведённым».

## Почему опасен

1. Нет исключения, нет логов — тихий partial write выглядит как успех.
2. Опубликованные файлы содержат обрезанный контент: 13 из 38 файлов в продакшене были затронуты.
3. Повторный прогон без проверки перезапишет теми же частичными данными.

## Лечение

- Всегда проверять `finish_reason` перед записью в файл: `if finish_reason == "length" → raise / retry / chunk`.
- Для длинных документов: вычислять ожидаемый размер вывода и выставлять `max_tokens` с запасом, не произвольно.
- Acceptance-тест пайплайна: файл после записи не должен обрываться на полуслове (простой byte-count / line-count check).

## Обнаружение

Тест: «что происходит, если документ вдвое длиннее обычного?» Если ответ «не знаем» → FM активен.

## Связи

- DP.FM.243 — детерминированный расчёт делегирован LLM (смежный класс тихих ошибок LLM-pipeline)
