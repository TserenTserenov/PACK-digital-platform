---
id: DP.FM.277
name: "Dispatch по структурному тегу вместо содержимого секции: ветка никогда не срабатывает"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-13
source: "session-close 2026-07-09; WP-7 Block DOF (DS-my-strategy commit 2d103df8b)"
related:
  see_also: ["DP.FM.260: path resolution masked as logic error (смежный класс: тихие ошибки в проверках)"]
tags: [dispatch, silent-wrong-branch, tag-vs-content, llm-fill, agent-pipeline]
---

# DP.FM.277 — Dispatch по структурному тегу: ветка обработки никогда не срабатывает

## Паттерн

Функция-диспетчер разбивает контент на части по структурному тегу (`<details>`, `##`, XML-тег).
Переменная `header` содержит голый тег, а не название секции.
Условие `if "section_name" in header` никогда не истинно.
Все секции идут по общему (default) пути — без ошибки, без исключения.

Класс: **silent wrong-branch** — нет exception, нет failure, внешне «работает».

## Пример

```python
# Антипаттерн
chunks = content.split("<details>")
for chunk in chunks:
    header = "<details>"          # голый тег, не содержимое!
    if "today_plan" in header:    # никогда True
        process_with_strong_model(chunk)
    else:
        process_with_cheap_model(chunk)   # всегда здесь

# Fix: извлечь содержимое из тега
import re
for chunk in chunks:
    match = re.search(r'<summary>(.*?)</summary>', chunk)
    header = match.group(1) if match else ""
    if "today_plan" in header:
        process_with_strong_model(chunk)
    else:
        process_with_cheap_model(chunk)
```

## Инцидент

WP-7 Block DOF (`day-open-llm-fill.py`, commit 2d103df8b):
ветка `today_plan` (Sonnet, сложная модель) никогда не активировалась — таблица плана дня
всегда уходила к дешёвой модели и получала шаблонные заглушки (`NNN`, `X`) вместо реальных данных.
Обнаружено при `/vdv audit` (стадия без потребителя Выхода внутри контура).

## Диагностика

**Тест:** «При любом dispatch-по-имени-секции — содержит ли переменная именно имя секции, или структурный тег?»
Проверить: `print(repr(header))` до условия.

**Сигнал:** Ветка с особой обработкой ни разу не логируется в продакшн-прогонах.

## Применимость

Любой парсинг Markdown/HTML/XML с разветвлением по имени секции.
