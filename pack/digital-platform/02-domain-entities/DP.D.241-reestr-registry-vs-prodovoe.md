---
id: DP.D.241
name: "Реестр (registry) ≠ Продовое состояние (production state)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-12
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.241: Реестр (registry) ≠ Продовое состояние (production state)

| Реестр | Продовое состояние |
|--------|-------------------|
| Декларативная запись о намерении/факте | Фактически задеплоенное и работающее |
| Обновляется вручную или CI-pipeline | Меняется при каждом deploy/config |
| Может отставать от реальности | Всегда текущее (даже если нет записи) |
| Источник: команда/агент | Источник: production environment |

**Почему важно:** механизм может быть задеплоен в production (работает, отвечает) без записи в реестр. Перед реализацией «нового» механизма — проверить production-baseline (live e2e тест, grep по деплоям), не только реестр. Ошибка: ArchGate и двойная реализация, основанная на устаревшем реестре.

**Тест:** «Если реестр говорит — не существует, проверял ли ты production?» Нет → drift-риск.

**Источник:** WP-5 MCP tool discovery, session-close 2026-07-07. Инструмент динамического discovery задеплоен с мая — реестр (executor-catalog.yaml / MAP.002) не знал.

**Смежно:** [DP.FM.202] (multiple-registries-one-entity-drift), [DP.FM.246] (stale-active-wp-in-memory-table).
