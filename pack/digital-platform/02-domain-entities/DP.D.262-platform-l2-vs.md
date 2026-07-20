---
id: DP.D.262
name: "Platform (L2) ≠ IWE Template (L3) ≠ Personal IWE (L4) — контуры системы"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-23
created: 2026-07-19
renamed_from: DP.D.034
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.034]
schema_version: 1
---

# DP.D.262: Platform (L2) ≠ IWE Template (L3) ≠ Personal IWE (L4) — контуры системы

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.034 → DP.D.262.** Номер 034 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.034» до 2026-07-19 могли означать эту сущность.

| Platform (L2) | IWE Template (L3) | Personal IWE (L4) |
|---------------|--------------------|--------------------|
| Инфраструктура и сервисы | Шаблон/формат для пользователя | Экземпляр, настроенный пользователем |
| Бот, MCP, Neon, Knowledge Index | CLAUDE.md, memory/, стратег-агент | Личные Pack, DS-strategy, планы |
| Обновляется разработчиком платформы (M2) | Обновляется разработчиком шаблона (M3) | Обновляется пользователем IWE (M4) |
| Pack DP + MAP.002 | FMT-exocortex-template | Личный CLAUDE.md |

**Почему важно**: Разработка/интеграция без указания контура → IntegrationGate нарушен. Правила, роли и процессы различаются по контурам. Одна и та же сущность (WakaTime) рекомендуется на L3, но настраивается на L4.

**Тест**: Затрагивает всех пользователей? → L2 (Platform). Только шаблон? → L3 (Template). Только конкретного пользователя? → L4 (Personal).

> Подробно: DP.EXOCORTEX.001 §11, DS-ecosystem-development/11-platform-contours.md
