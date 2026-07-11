---
id: DP.FM.255
name: "Transitive shim dependency: пакет используется только как HTTP-клиент для стороннего провайдера, блокирует CI"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-07-10
source: "captures/2026-07-06 feed:session-close; FMT-exocortex-template translate.py commit b3ba993"
related:
  references: [DP.FM.234]
  see_also: ["DP.FM.234 — provider migration tail offline scripts"]
tags: [dependency, ci, import, shim, hidden-dependency]
---

# DP.FM.255 — Transitive shim dependency: пакет используется только как HTTP-шим для стороннего провайдера, блокирует CI

## Паттерн

Код импортирует пакет A (например, `openai`) только для использования его как HTTP-клиента с OpenAI-совместимым интерфейсом провайдера B (например, OpenRouter). Пакет A не нужен для API провайдера B, но присутствует в `import` — что создаёт скрытую CI-зависимость.

## Пример

```python
# translate.py — работает только с OpenRouter, не с OpenAI
from openai import OpenAI  # использован как HTTP-шим, не для OpenAI API

client = OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=OPENROUTER_API_KEY,
)

# При деплое в CI: pip install не включает openai → ImportError → CI блокирован
```

## Механизм

Разработчик выбрал `openai` SDK как удобный HTTP-клиент (OpenAI-совместимый интерфейс), не имея в виду использовать OpenAI API. При деплое или CI-шаге без `openai` в зависимостях — `ImportError` блокирует весь пайплайн. Ошибка незаметна до первого деплоя в среду без пакета.

## Почему опасен

1. Блокирует CI целиком, не только модуль: одна скрытая зависимость = весь деплой стоит.
2. Мотивация держать пакет размыта («но мы же используем его»), хотя реальная функция — stdlib-замена.
3. При смене провайдера пакет A остаётся в зависимостях без явного владельца.

## Лечение

- Тест: «пакет используется исключительно как HTTP-клиент для стороннего API?» Да → заменить на `httpx` или `urllib.request`.
- При добавлении зависимости: проверять, нужен ли родной SDK провайдера или достаточно HTTP-запроса.
- В CI: `pip check` или `pipdeptree` для обнаружения пакетов без прямых вызовов нативного API.

## Обнаружение

Grep: `import <package>` существует, но `<package>.<PackageClass>()` вызывается с `base_url` стороннего провайдера → подозрение на шим.

## Связи

- DP.FM.234 — provider migration tail offline scripts (смежный класс: тихие хвосты при миграции провайдера)
