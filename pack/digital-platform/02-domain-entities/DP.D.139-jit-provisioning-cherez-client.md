---
id: DP.D.139
name: "JIT provisioning через client-mediated flow ≠ direct-link flow"
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

# DP.D.139: JIT provisioning через client-mediated flow ≠ direct-link flow

JIT provisioning (создание аккаунта на лету при первом обращении) реализуется на разных уровнях стека. Ключевое различение — где живёт provisioning-логика:

| Client-mediated flow | Direct-link flow |
|---------------------|-----------------|
| Пользователь приходит через посредника (MCP, OAuth callback, SSO relay) | Пользователь открывает ссылку входа напрямую |
| Посредник вызывает provisioning-endpoint ДО redirect на identity-форму | Провайдер не знает о намерении создать аккаунт — форма появляется сразу |
| Нового пользователя создают автоматически | Нового пользователя форма не создаёт — только authenticate |
| Пример: MCP flow через claude.ai → Ory создаёт аккаунт | Пример: прямая ссылка `/login` → Ory не знает, что аккаунта нет |

**Инвариант:** JIT provisioning работает только там, где стоит interceptor/посредник.

**Тест:** «Выполняется ли provisioning, если пользователь приходит без посредника?» Нет → асимметрия flow'ов (риск DP.FM.159). Требуется либо выносить provisioning на уровень identity-провайдера, либо явное UX-разделение двух entry-point'ов.

**Источник:** sessions/2026-06/2026-06-13-ory-registration-ux-fix.md, 2026-06-14
