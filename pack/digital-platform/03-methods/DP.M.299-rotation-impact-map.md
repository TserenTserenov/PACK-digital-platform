---
id: DP.M.299
name: "Rotation impact map: инвентаризация мест секрета до ротации"
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: security-operations
valid_from: 2026-06-08
related:
  see_also: []
tags: [security, secret-rotation, api-key, oauth, impact-map, multi-service, inventory, dry-run]
source: "session 2026-06-08, git diff DS-my-strategy (d2414bff3, fix wp-399 rotate OpenRouter key + 660a713e1, WP-212 rotation impact map)"
schema_version: 1
---

# DP.M.299 — Rotation impact map

## Описание

Артефакт и практика: перед ротацией любого секрета создать и поддерживать полную карту всех мест, где этот секрет используется. Пробел в карте = молчаливый сбой при ротации.

## Проблема без impact map

Новый ключ разложен в 4 из 5 мест → одна точка продолжает использовать старый ключ → end-to-end проверка провалится только на этой точке. Остальные 4 точки покажут успех — ложное ощущение завершённости.

## Структура impact map

```
Секрет: OpenRouter API Key
├── ~/.zshrc (2 строки — OPENROUTER_KEY + alias)
├── ~/.openrouter.yaml
├── Railway: iwe-llm-proxy (env var)
└── tsekh: session-dispatcher.env
```

## Процесс

1. При регистрации нового секрета — сразу создать/обновить impact map
2. Перед ротацией — пройти по карте явно, отметить каждое место
3. End-to-end проверка — включает все N мест из карты

## Применимость

API-ключи, OAuth-секреты, DB-пароли в multi-service окружении. Обязательно для секретов, используемых в ≥ 3 местах.

## Тест

«Знаю ли я все места, где живёт этот секрет?» Не уверен → создать impact map перед ротацией.
