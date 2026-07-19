---
id: DP.D.134
name: "Logout (Kratos login-сессия) ≠ Отзыв OAuth-grant (Hydra)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-14
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.134: Logout (Kratos login-сессия) ≠ Отзыв OAuth-grant (Hydra)

**Logout / «Выйти со всех устройств»** (Kratos `settings/logout`) — гасит login-сессии пользователя на платформе. Управляет методами входа, не выданными разрешениями.

**Отзыв OAuth-grant** (Hydra admin API + RFC 7009 revoke) — отзывает refresh-токены/grants, выданные внешним клиентам (напр., claude.ai MCP). Требует предъявить сам токен; у пользователя обычно нет прямого доступа.

**Следствие:** Reconnect MCP-коннектора переиспользует живой grant — окна входа не появляется даже после logout.

**Тест:** «После logout и переподключения показалось окно входа?» Нет → grant жив, сессия не прервалась.

**UI-следствие:** Раздел «Управление доступом» нужно делить на две секции: «Активные сессии входа» (Kratos) и «Разрешения приложений» (Hydra), с разными API для каждой.

**Источник:** WP-411 Ф5, peer-session 2026-06-12-01.
