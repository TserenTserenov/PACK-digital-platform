---
id: DP.M.304
name: "Локальный импорт тяжёлой зависимости для optional backend"
type: method
status: draft
created: 2026-06-12
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: established
sources:
  - session-close 2026-06-11, WP-417 Ф3.2 (panel_store.py:__init__, eeaee81d6)
tags: [python, import, optional-backend, lazy-import, dev-prod-parity]
wp: WP-417
---

# DP.M.304 — Локальный импорт тяжёлой зависимости для optional backend

## 1. Проблема

Модуль поддерживает два backend'а: production-only зависимость + lightweight для dev/test. Module-level import тяжёлой зависимости → `ImportError` на dev-машине → тесты не запускаются.

## 2. Паттерн

```python
class PanelStore:
    def __init__(self, dsn: str | None = None):
        self.is_pg = dsn is not None
        if self.is_pg:
            import psycopg2  # локальный импорт: dev/тест без psycopg2 не падает
```

**Свойства:** Python кэширует модули → повторные `import` = `sys.modules` lookup, overhead минимален.

## 3. Trade-off

| Аспект | Module-level import | Local import (этот паттерн) |
|--------|--------------------|-----------------------------|
| Fail fast | При startup | При первом pg-вызове |
| Dev без зависимости | ImportError блокирует всё | Работает |
| Читаемость | Imports в одном месте | Зависимость скрыта в методе |

## 4. Применимость

- Optional DB drivers (psycopg2/asyncpg/mysqlclient)
- Heavy ML libs (torch/transformers) — только когда нужна модель
- Cloud SDK — только когда задан DSN провайдера

## 5. Антипаттерн

```python
# ПЛОХО — скрывает fail до момента использования
try:
    import psycopg2
except ImportError:
    psycopg2 = None  # AttributeError в рантайме, не при конфигурации
```

## 6. Различение

**≠ DP.M.150 (multi-driver duck-typing):** DP.M.150 — совместимость двух версий одного драйвера (оба доступны). Этот паттерн — одна из зависимостей полностью отсутствует в dev-окружении.

## 7. Связи

- DP.M.150 — multi-driver compat (смежная задача: разные версии vs разные окружения)
- DP.M.020 — optional dependency via params.yaml (shell-аналог)
