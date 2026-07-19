---
id: DP.D.153
name: "ICT-токен (ict_...) ≠ Ory OAuth (claude.ai / VS Code коннектор)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-19
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.153: ICT-токен (ict_...) ≠ Ory OAuth (claude.ai / VS Code коннектор)

| Аспект | ICT-токен | Ory OAuth (OIDC) |
|--------|-----------|-----------------|
| **Формат** | `ict_...` строка | Ory refresh_token / JWT access_token |
| **Клиент** | Claude Code CLI (`.mcp.json`) | claude.ai, VS Code extension |
| **Получение** | `POST /internal/auth/exchange` + auth-код из бота | OIDC Authorization Code flow через Hydra |
| **Видимость** | Показывается в `/my_clients` бота | НЕ показывается в `/my_clients` |
| **Хранение** | `.mcp.json` в проекте | Ory token store |
| **Без токена** | Claude Code CLI ходит как T0 (базовый набор, без тира) | Нет доступа к gateway |

**Тест различения:** «Виден ли мой коннект в `/my_clients` бота?»
- Да → ICT-токен (Claude Code CLI)
- Нет → Ory OAuth (claude.ai / VS Code)

**Почему важно:** Без `Authorization: Bearer ict_...` в `.mcp.json` Claude Code CLI работает как анонимный T0 — получает базовый набор инструментов без персонализации и тировых возможностей. ICT и Ory — независимые механизмы авторизации, не взаимозаменяемые.

**Дополнение (JWT кэш):** Ory JWT кэшируется ~1 час и может содержать устаревший тир. Когда JWT говорит T1 — gateway обязан делать DB-fallback для актуального тира пользователя.

**Связано с:** [DP.D.087] (OAuth pending state in-memory ≠ externalized), [DP.FM.153] (intermittent 401 static key).

**Источник:** session WP-5 (2026-06-18-ict-token-mcp-auth).
