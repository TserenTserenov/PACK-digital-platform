---
id: DP.M.149
name: "Bearer == Shared Secret Backward-Compatible Auth Mode"
name_ru: "Bearer-заголовок как третий auth-mode: обратно-совместимое расширение"
type: method
status: draft
created: 2026-05-22
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: established
related:
  uses: []
  see_also: [DP.D.059]
tags: [auth, proxy, gateway, backward-compatible, bearer, oauth]
wp: WP-200 Ф7 (2026-05-22)
---

# Bearer-заголовок как третий auth-mode: обратно-совместимое расширение (DP.M.149)

## 1. Проблема

Прокси или gateway поддерживает несколько auth-каналов (shared_secret и JWT/OAuth). При добавлении нового канала нежелательно ломать существующих клиентов и создавать отдельные эндпоинты.

## 2. Паттерн

Использовать `Authorization: Bearer <token>` как унифицированный заголовок и определять mode по **форме токена**:

```python
token = request.headers.get("Authorization", "").removeprefix("Bearer ")
if token.startswith("eyJ"):
    verify_jwt(token)          # JWT mode — OAuth/Ory
elif len(token) == SHARED_SECRET_LENGTH:
    verify_hmac(token)         # shared_secret mode — legacy/internal
else:
    raise AuthError("unrecognized token format")
```

Три mode на одном заголовке:
1. Basic (`Authorization: Basic base64(user:pass)`) — существующий
2. Bearer JWT (`Authorization: Bearer eyJ...`) — OAuth/Ory
3. Bearer shared_secret (`Authorization: Bearer <secret>`) — новый, обратно-совместимый

## 3. Когда применять

- Существующие клиенты используют shared_secret, переход на OAuth постепенный.
- Core-логика прокси одна, нужно поддерживать N auth-каналов без версионирования эндпоинтов.
- Новые клиенты (n8n credentials, exocortex) должны передавать токен в стандартном Bearer-заголовке.

## 4. Антипаттерны

- **Отдельные эндпоинты** (`/v1/internal` vs `/v1/oauth`) — разрастание surface и дублирование middleware.
- **Auth-mode через query-параметр** (`?auth=secret`) — утечка секрета в logs.
- **Параллельные заголовки** (`X-Internal-Token` + `Authorization`) — два несвязанных auth-механизма.

## 5. Условия применимости

- Форма токенов предсказуема (JWT начинается с `eyJ`, shared_secret — фиксированная длина/префикс).
- Middleware умеет различать mode по форме токена до верификации.

## 6. Связи

- **DP.D.059** — Three classes of credentials storage (хранение на клиенте)
