---
id: DP.M.214
name: "Silent OAuth Token Provisioning — провиженинг через session cookie"
type: method
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-transcript 2026-05-28, WP-355 IWE browser extension"
---

# DP.M.214 Silent OAuth Token Provisioning

## Описание

UX-паттерн для браузерных расширений и десктоп-клиентов: автоматическое получение API-токена при наличии активной web-сессии пользователя без ручных шагов (копировать/вставить токен).

## IPO

**Вход:** пользователь залогинен в платформе (есть Ory session cookie); расширение только что установлено
**Процесс:** `GET /api/<service>/token` с session cookie → платформа верифицирует через Ory → возвращает `{token, endpoint}`
**Выход:** токен сохранён локально в расширении; ноль ручных действий от пользователя

## Технические требования

- CORS `Allow-Origin: chrome-extension://*` на `GET /api/<service>/token`
- Endpoint верифицирует Ory-сессию через `X-Session-Token` или cookie
- Токен — одноразовый или с TTL (не long-lived)

## Антипаттерн

Показывать токен в боте/UI с просьбой скопировать вручную → нереалистичный UX, пользователь не понимает зачем.

## Тест применимости

«Пользователь уже авторизован в web-сессии платформы?» Да → применять DP.M.214.

## Применение

- IWE browser extension получает токен для Local Gateway
- Десктоп-клиент получает API-ключ после OAuth login
- Любое расширение, интегрированное с OAuth-платформой

## Связи

- Источник: WP-355 IWE browser extension setup, 2026-05-28
