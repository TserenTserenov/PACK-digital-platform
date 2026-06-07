---
id: DP.M.291
type: method
title: "patch.object вместо строкового пути при реэкспорте имени через __init__.py"
slug: patch-object-vs-string-path-mock
domain: digital-platform
trust: high
epistemic_stage: observed
valid_from: 2026-06-06
source: session-transcript 2026-06-06, WP-392 Ф3.1b; commit tests/smoke/test_t4_full.py
related: []
schema_version: 1
---

# DP.M.291 — patch.object вместо строкового пути при реэкспорте через __init__.py

## Описание

Метод выбора правильного места для мокирования в Python-тестах, когда пакет реэкспортирует имя через `__init__.py`.

## Проблема

`unittest.mock.patch("a.b.c")` разрешает путь: ищет модуль `a.b`, достаёт атрибут `c`. Если `a/__init__.py` делает `from a.b import c`, тест импортирует через `from a import c` — реальный объект живёт в `a.b`, но патч ищет в `a`. Патч применяется не к источнику → мок не работает (assert_called_once() = False), тест «зелёный», но реальный код вызывает оригинал.

## Решение

```python
import importlib
_mod = importlib.import_module("a.b")
with patch.object(_mod, "c") as mock_c:
    # теперь патч применён к источнику
    ...
```

## Тест применимости

«Пакет реэкспортирует имя через `__init__.py`?» + «Мок не вызывается при assert_called_once()?» → применить `patch.object`.

## Связи

- Принцип: патчить там, где объект живёт, а не там, где импортируется.
