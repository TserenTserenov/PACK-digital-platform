---
id: DP.FM.127
name: "Python 3.9: тип-аннотации → TypeError без from __future__ import annotations"
type: failure-mode
pack: PACK-digital-platform
domain: python-compat
trust: 0.9
epistemic_stage: confirmed
valid_from: 2026-06-01
source: "WP-379 Ф6 (DS-autonomous-agents, commit cf0ff2a, render-pilot-guides.py)"
tags: [python, compat, type-hints, python39]
schema_version: 1
---

# DP.FM.127 — Python 3.9: тип-аннотации → TypeError без `from __future__ import annotations`

## Класс ошибки

Python 3.10+ поддерживает PEP 604 (`X | Y`) и PEP 585 (`list[str]`) нативно в type hints.
Python 3.9 — нет: использование `X | None` или `list[str]` в аннотациях вызывает `TypeError` при runtime.

При этом статический анализ (mypy, pyright) на Python 3.10+ не ловит проблему — ошибка
проявляется только на prod (Railway/Docker с `python:3.9` base image).

## Диагноз

- `TypeError: unsupported operand type(s) for |: 'type' and 'NoneType'` при вызове функции с `X | None`
- `TypeError: 'type' object is not subscriptable` при `list[str]` в аннотации
- Ошибка в runtime, не в import-time

## Фикс

Добавить `from __future__ import annotations` **первой строкой** модуля (PEP 563 — lazy evaluation).

```python
from __future__ import annotations  # первая строка модуля
```

Нулевая стоимость: производительность и поведение не меняются.

## Правило

Все Python-модули с type hints в проекте, где prod среда = Python ≤3.9, обязаны иметь эту строку.

## Связанные паттерны

- `memory/lessons_python39_pipe_union_type.md` — memory-урок (quick ref)
- DP.FM.128 — Pytest collection error при missing module attribute (смежный Python 3.9 кейс)
