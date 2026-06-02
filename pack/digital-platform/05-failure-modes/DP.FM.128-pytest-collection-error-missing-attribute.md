---
id: DP.FM.128
name: "Pytest: тест не запускается из-за ImportError при collection (Python ≤3.9)"
type: failure-mode
pack: PACK-digital-platform
domain: python-compat
trust: 0.9
epistemic_stage: confirmed
valid_from: 2026-06-01
source: "WP-379 Ф6 review fix (DS-autonomous-agents, commit a1d23bf, tests/)"
tags: [python, pytest, compat, testing, python39, collection-error]
schema_version: 1
---

# DP.FM.128 — Pytest: тест не запускается из-за ImportError при collection

## Класс ошибки

Python ≤3.9 не имеет некоторых module-level атрибутов, добавленных в 3.10+. При `pytest`:

- Импорт тестируемого модуля во время **collection фазы** → `ImportError`
- Тест **не падает** — он «не существует» (`ERROR collecting` ≠ `FAILED`)
- CI «зелёный», хотя тест никогда не запускался и ничего не проверял

## Диагноз

```bash
pytest --collect-only 2>&1 | grep "ERROR collecting"
```

`ERROR collecting test_foo.py` → ImportError при collection, не assertion error при run.

## Фикс

В `conftest.py` stub недостающий атрибут **до** импорта тестируемого модуля (порядок критичен):

```python
import sys, types

mod = sys.modules.setdefault('target_module', types.ModuleType('target_module'))
if not hasattr(mod, 'missing_attr'):
    mod.missing_attr = default_value
# ← только после этого: import target_module
```

## Правило

Паттерн применим к: conditional imports, `TYPE_CHECKING` блокам, lazy initializers.

## Связанные паттерны

- DP.FM.127 — Python 3.9 type hint compat (смежный Python 3.9 кейс)
