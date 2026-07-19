---
id: DP.D.200
name: "FORCE ROW LEVEL SECURITY ≠ Защита от роли с атрибутом BYPASSRLS"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-10
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.200: FORCE ROW LEVEL SECURITY ≠ Защита от роли с атрибутом BYPASSRLS

**Контекст:** PostgreSQL, Row-Level Security, управляемые облачные базы (Neon, RDS).

**Различение:**
- **FORCE ROW LEVEL SECURITY** — заставляет RLS применяться к **владельцу таблицы** (который по умолчанию обходит собственные политики). Лечит: «хозяин таблицы видит все строки».
- **Роль с атрибутом BYPASSRLS** — обходит RLS при любых условиях, включая FORCE ROW LEVEL SECURITY. FORCE защищает только от первого механизма и не влияет на второй.

**Следствие:** добавить `FORCE ROW LEVEL SECURITY` недостаточно, если к базе подключается роль с атрибутом `BYPASSRLS` (например, управляемая облаком `neondb_owner`, у которой `ALTER ROLE ... NOBYPASSRLS` недоступен). Два механизма обхода → два разных решения.

**Тест:** «Есть ли `FORCE ROW LEVEL SECURITY` → защита полная?» Нет — если хотя бы одна подключающаяся роль имеет `BYPASSRLS`.

**Фикс для BYPASSRLS-роли:** разделение ролей (отдельная роль без атрибута для приложения) или программная фильтрация данных в коде приложения поверх BYPASSRLS-роли.

**Связано:** DP.D.199 (RLS-политика ≠ защита для роли с BYPASSRLS — общий принцип).

**Источник:** WP-417 Ф6 + WP-457 Ф10 (2026-07-05), peer-session.
