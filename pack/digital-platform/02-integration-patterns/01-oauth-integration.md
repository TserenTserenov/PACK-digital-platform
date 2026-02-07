# OAuth-интеграция с внешними сервисами

## Контекст

Цифровой двойник интегрируется с внешними сервисами (Linear, Google Calendar, Notion и др.) через OAuth 2.0. Пользователь авторизует доступ один раз, после чего двойник может читать/писать данные от его имени.

## Разделение ответственности

### Разработчик (один раз)

1. **Регистрация OAuth-приложения** в сервисе:
   - Название приложения
   - Redirect URI: `https://<your-domain>/auth/<service>/callback`
   - Запрашиваемые scopes (права доступа)

2. **Получение credentials**:
   - `CLIENT_ID` — публичный идентификатор
   - `CLIENT_SECRET` — секрет (хранить в env-переменных)

3. **Настройка инфраструктуры**:
   - Callback endpoint для обработки OAuth redirect
   - Хранилище токенов (Redis/DB с шифрованием)

### Пользователь (каждый раз)

1. Инициирует подключение (кнопка/команда)
2. Переходит по ссылке авторизации
3. Подтверждает доступ в интерфейсе сервиса
4. Автоматически возвращается обратно

## Authorization Code Flow

```
┌─────────┐     ┌──────────┐     ┌─────────────┐
│  User   │     │  Your    │     │  External   │
│         │     │  App     │     │  Service    │
└────┬────┘     └────┬─────┘     └──────┬──────┘
     │               │                  │
     │ 1. /connect   │                  │
     ├──────────────>│                  │
     │               │                  │
     │ 2. Auth URL   │                  │
     │<──────────────┤                  │
     │               │                  │
     │ 3. Redirect   │                  │
     ├─────────────────────────────────>│
     │               │                  │
     │ 4. User authorizes               │
     │<─────────────────────────────────┤
     │               │                  │
     │ 5. Callback with code            │
     ├──────────────>│                  │
     │               │                  │
     │               │ 6. Exchange code │
     │               ├─────────────────>│
     │               │                  │
     │               │ 7. Access token  │
     │               │<─────────────────┤
     │               │                  │
     │ 8. Success    │                  │
     │<──────────────┤                  │
```

## Компоненты реализации

### 1. Генерация URL авторизации

```python
def get_authorization_url(user_id: int) -> tuple[str, str]:
    """
    Returns:
        (auth_url, state) — URL для редиректа и state для верификации
    """
    state = secrets.token_urlsafe(32)

    # Сохраняем state -> user_id (TTL 10 минут)
    pending_states[state] = {
        "user_id": user_id,
        "created_at": time.time()
    }

    params = {
        "client_id": CLIENT_ID,
        "redirect_uri": REDIRECT_URI,
        "response_type": "code",
        "scope": ",".join(SCOPES),  # или space-separated
        "state": state,
    }

    return f"{AUTHORIZE_URL}?{urlencode(params)}", state
```

### 2. Callback handler

```python
async def oauth_callback(request):
    code = request.query.get("code")
    state = request.query.get("state")
    error = request.query.get("error")

    if error:
        return error_page(error)

    # Валидация state
    user_id = validate_state(state)
    if not user_id:
        return error_page("Session expired")

    # Обмен code на token
    tokens = await exchange_code(code)
    if not tokens:
        return error_page("Token exchange failed")

    # Сохраняем токены
    save_tokens(user_id, tokens)

    # Уведомляем пользователя
    await notify_user(user_id, "Connected!")

    return success_page()
```

### 3. Обмен code на token

```python
async def exchange_code(code: str) -> dict | None:
    payload = {
        "grant_type": "authorization_code",
        "client_id": CLIENT_ID,
        "client_secret": CLIENT_SECRET,
        "redirect_uri": REDIRECT_URI,
        "code": code,
    }

    async with aiohttp.ClientSession() as session:
        async with session.post(
            TOKEN_URL,
            data=payload,
            headers={"Content-Type": "application/x-www-form-urlencoded"}
        ) as resp:
            if resp.status == 200:
                return await resp.json()
            return None
```

### 4. Использование токена для API-вызовов

```python
async def api_call(user_id: int, endpoint: str) -> dict | None:
    access_token = get_access_token(user_id)
    if not access_token:
        return None

    async with aiohttp.ClientSession() as session:
        async with session.get(
            f"{API_BASE_URL}/{endpoint}",
            headers={"Authorization": f"Bearer {access_token}"}
        ) as resp:
            if resp.status == 200:
                return await resp.json()
            return None
```

## Чек-лист настройки нового сервиса

- [ ] Зарегистрировать OAuth-приложение в сервисе
- [ ] Добавить env-переменные: `{SERVICE}_CLIENT_ID`, `{SERVICE}_CLIENT_SECRET`
- [ ] Настроить redirect URI в приложении сервиса
- [ ] Создать endpoint `/auth/{service}/callback`
- [ ] Определить необходимые scopes
- [ ] Реализовать хранение токенов (с TTL для refresh)
- [ ] Добавить команду/кнопку подключения для пользователя
- [ ] Обработать refresh token (если сервис его выдаёт)

## Особенности разных сервисов

| Сервис | Scopes format | Refresh token | Примечания |
|--------|---------------|---------------|------------|
| Linear | comma-separated | Нет (public apps) | GraphQL API |
| Google | space-separated | Да | Требует consent screen |
| Notion | — | Да | Workspace-level access |
| GitHub | comma-separated | Нет | Granular permissions |

## Безопасность

1. **State parameter** — обязателен для защиты от CSRF
2. **HTTPS** — redirect URI только через HTTPS
3. **Хранение секретов** — только в env-переменных, не в коде
4. **Токены** — шифровать в БД, минимальный TTL
5. **Scopes** — запрашивать минимально необходимые права
