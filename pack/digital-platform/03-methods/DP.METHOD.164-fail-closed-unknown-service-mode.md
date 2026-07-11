---
id: DP.METHOD.164
type: method
domain: PACK-digital-platform
status: draft
summary: "При отсутствующем или неизвестном значении параметра режима сервиса — отказать с явной ошибкой (HTTP 500 / exit 1), не делать fallback в публичный режим. Тихий fallback = fail-open при ошибке деплоя."
created: 2026-06-30
valid_from: 2026-06-30
version: v1.0
source: "session-transcript 2026-06-30-05-wp410-mcp-unification + decision-log-2026-06.md (решение 2)"
related:
  see_also: [DP.METHOD.091, DP.METHOD.092, DP.M.298]
---

# DP.METHOD.164: Fail-closed при неизвестном режиме сервиса

## Назначение

Паттерн обработки неизвестного или отсутствующего значения конфигурационного параметра,
определяющего режим доступа сервиса.

## Правило

При неизвестном или отсутствующем значении `MODE` (public/personal/admin/...) сервис:

1. Возвращает явную ошибку: HTTP 500 + лог «unknown mode '{VALUE}', refusing to start»
2. **Не выполняет** автоматический fallback в наименее привилегированный (публичный) режим

## Почему не fallback-в-public

| Поведение | При опечатке или сброшенном env |
|-----------|--------------------------------|
| Fallback в public | Сервис запускается, отдаёт данные без авторизации |
| Fail-closed (500 + log) | Сервис не запускается, ошибка видна немедленно |

Автоматический fallback создаёт fail-open: при любой ошибке деплоя сервис работает
в наименее ограниченном режиме — и может раскрывать данные.

## Применение

```python
MODE = os.getenv("MCP_MODE")  # "public" | "personal"
KNOWN_MODES = {"public", "personal"}
if MODE not in KNOWN_MODES:
    logger.error("unknown mode %r, refusing to start", MODE)
    raise SystemExit(1)
```

## Область применения

Любой сервис с явным enum режимов доступа:

- MCP-сервер с personal/public scope
- API-gateway с уровнями доступа L1/L2/L3
- Worker-процесс с safe/unsafe режимами
- Мультитенантный сервис с tenant-specific конфигурацией

## Тест

«`MODE` не задан или содержит опечатку — что делает сервис?»

- Тихий старт в public режиме → fail-open → неверно
- HTTP 500 / exit(1) с «unknown mode» → fail-closed → верно

## Связи

- DP.METHOD.091 — auth layering (выбор базы при слиянии public/private сервисов)
- DP.METHOD.092 — CI allowlist (защита от нежелательных bindings в конфиге)
- DP.M.298 — fail-closed-scope-sidecar (другой механизм fail-closed через sidecar)
