---
id: DP.FM.257
name: "Railway project-scoped токен: неверный заголовок `Bearer` вместо `Project-Access-Token` → 401"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-07-10
source: "captures/2026-07-07 feed:session-close; DS-my-strategy commit 2d5753ead, wp-7 L3-AUTH1"
related:
  references: [DP.FM.228]
  see_also: ["DP.FM.228 — Railway liveness probe auth blocks"]
tags: [railway, api, auth-header, project-token, 401]
---

# DP.FM.257 — Railway project-scoped токен: неверный заголовок `Bearer` вместо `Project-Access-Token` → 401

## Паттерн

Railway API возвращает 401 не из-за неверного токена, а из-за неверного типа заголовка авторизации. Project-scoped токены требуют заголовок `Project-Access-Token`, тогда как стандартный `Authorization: Bearer` работает только для team-scoped токенов.

## Пример

```python
# Неверно (project-scoped токен через Bearer → 401):
headers = {"Authorization": f"Bearer {RAILWAY_PROJECT_TOKEN}"}

# Верно (project-scoped токен):
headers = {"Project-Access-Token": RAILWAY_PROJECT_TOKEN}

# Команды работают только с team-scoped токенами:
headers = {"Authorization": f"Bearer {RAILWAY_TEAM_TOKEN}"}
```

## Механизм

L3 auto-restart сервиса использовал `Bearer` заголовок. Токен был project-scoped. Railway API молча отклонял с 401, сообщение об ошибке не указывало на тип заголовка. Диагностика через живой curl показала: токен валиден, проблема в заголовке.

## Почему опасен

1. Сообщение об ошибке 401 не разграничивает «неверный токен» и «неверный тип заголовка».
2. Тест «токен существует и не истёк» проходит; ошибка не обнаруживается до живого запроса.
3. Блокирует критическую инфраструктуру (auto-restart L3).

## Лечение

- При работе с Railway API: определить тип токена (team vs project) и использовать соответствующий заголовок.
- Smoke-тест: живой curl к Railway API с текущими credentials ДО интеграции в автоматику.
- В документации к инфраструктуре: хранить рядом с токеном его тип и нужный заголовок.

## Обнаружение

Тест: `curl -H "Authorization: Bearer $TOKEN" https://backboard.railway.app/graphql/v2` — если 401 при валидном токене, подозрение на тип заголовка.

## Связи

- DP.FM.228 — Railway liveness probe auth blocks (смежный: Railway auth, другой механизм)
