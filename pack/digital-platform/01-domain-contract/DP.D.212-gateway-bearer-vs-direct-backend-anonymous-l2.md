---
id: DP.D.212
name: "Gateway (требует Bearer для L2) ≠ Прямой бэкенд (отдаёт L2 анонимно)"
type: distinction
domain: digital-platform
pack: PACK-digital-platform
status: active
valid_from: 2026-06-22
schema_version: 1
source: "WP-149 B2 (ArchGate A2, anonymous prefetch flow), peer-12, commit 1104046, 2026-06-21"
related: [DP.D.031]
---

# DP.D.212 — Gateway (требует Bearer для L2) ≠ Прямой бэкенд (отдаёт L2 анонимно)

Gateway-слой требует `Authorization: Bearer <token>` даже для L2-публичного контента — без токена отклоняет запрос. Прямой бэкенд (knowledge-mcp backend) отдаёт L2-публичный контент анонимно: токен не нужен, верифицировано на живом инстансе.

**Следствие при проектировании:** перед реализацией anonymous-read flow (prefetch, анонимный поиск) проверить, не стоит ли gateway-барьер между клиентом и публичным знанием. Если стоит — выбрать прямой путь к бэкенду или добавить токен в запрос.

**Тест:** «Стоит ли gateway между запросом и публичным знанием?» Да → нужен Bearer. Нет → прямой запрос к бэкенду проходит анонимно.

**Связано с:** DP.D.031 (MCP access model).

**Источник:** WP-149 B2 (ArchGate A2, anonymous prefetch flow), peer-12, commit 1104046, 2026-06-21.
