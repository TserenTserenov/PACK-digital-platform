---
id: DP.SC.187
name: local-gateway-render
version: "1.0"
status: draft
created: "2026-06-30"
owner: WP-149
related_roles: []
related_wps: ["WP-149"]
---

# DP.SC.187 — Local Gateway: render personal guide

## Обещание

Local Gateway принимает запрос на сборку персонального руководства пилота и
возвращает статус выполнения и путь к готовому файлу. Сборка выполняется
Python-алгоритмом (`render-pilot-guides.py`) — единственная реализация; LLM
не участвует в алгоритме сборки (только в генерации текстовых слотов внутри скрипта).

## Триггер

Вызов инструмента `mcp__iwe-local-gateway__render_personal_guide` из Claude Code
сессии пилота.

## Входы

| Параметр | Тип | Обязателен | Описание |
|----------|-----|-----------|----------|
| `date` | `str` (ISO 8601, `YYYY-MM-DD`) | да | Дата для дневного руководства |
| `output_path` | `str` | нет | Абсолютный путь для записи результата; если не указан — дефолт из конфига gateway |

## Выходы

```json
{
  "status": "ok | warn | fail",
  "guide_path": "/abs/path/to/guide/daily/YYYY-MM-DD.md",
  "warnings": ["..."]
}
```

| Поле | Описание |
|------|----------|
| `status: ok` | Руководство собрано без деградации источников |
| `status: warn` | Собрано, но один или несколько источников деградировали (YAML-fallback) |
| `status: fail` | Сборка не выполнена; причина в `warnings` |

## Идентичность пилота

Pilot identity = конфиг gateway (`PILOT_ID` / `GITHUB_OWNER` из `EnvironmentFile`).
Параметр `pilot_id` в сигнатуре отсутствует намеренно — предотвращает межпилотный
доступ к данным при запуске на shared-машине.

## Инвариант

Python-скрипт `render-pilot-guides.py` — единственное место реализации алгоритма
сборки. Скилл `personal-guide-render` является тонким клиентом и **не дублирует**
логику сборки в промпте. Нарушение (дублирование алгоритма в prompt) = OwnerIntegrity
violation (peer-session 2026-06-30-06).

## Режим отказа

| Условие | Поведение |
|---------|-----------|
| Neon (knowledge DB) недоступен | `status: warn`, YAML-fallback для источников знаний |
| Neon (personal DB) недоступен | `status: fail`, сборка невозможна без профиля пилота |
| `render-pilot-guides.py` завершился с ненулевым кодом | `status: fail`, stderr в `warnings` |
| `output_path` недоступен для записи | `status: fail` |

## Время отклика

Целевое: ≤60 секунд (типичный дневной рендер ~30с на tsekh-1 по наблюдению 2026-06-30).

## Gateway URL

Задаётся через `params.yaml` пользователя (ключ `local_gateway.url`,
дефолт `http://localhost:8765`). Скилл читает значение из `{{LOCAL_GATEWAY_URL}}`
— не хардкод.

## Расширение service-scope local-gateway

Это расширение меняет характер local-gateway: из coordination-сервиса
(блокировки, статусы) — в compute-сервис (запускает Python, читает Neon, пишет markdown).
Расширение зафиксировано как архитектурное решение peer-session 2026-06-30-06.

## Связи

- `DP.SC.154` — peer-session (DP.SC.154), в рамках которой принято решение
- WP-149 Ф-local-assembly-delivery
- `DP.SC.NNN-local-gateway` (coordination scope, предшественник)
