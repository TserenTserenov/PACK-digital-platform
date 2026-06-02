---
id: DP.D.123
name: "State-Dependency Test для классификации skills"
type: distinction
domain: digital-platform
pack_refs: [DP.D.056, DP.D.073]
status: active
valid_from: 2026-06-01
schema_version: 1
source: "WP-387 skills placement + commit 9e200f582 (exocortex/memory/lessons_2026-06-01-skills-placement.md)"
---

# DP.D.123 — State-Dependency Test для классификации skills

## Различение

«Где живёт постоянное состояние, которое skill читает/пишет?» — операциональный критерий выбора места жительства skill'а (FMT vs Platform-интерфейс vs read-only-DS).

| Где state | Куда жить skill'у | Маркер | Канал запуска |
|-----------|-------------------|--------|---------------|
| Локальные файлы / git | FMT-exocortex-template (поставка через `update.sh`) | — | локальный CLI/IDE |
| Neon / Ory / GitHub App | FMT-exocortex-template | `platform_integration: true` | Aisystant MCP / Cloud Gateway |
| Stateless / read-only | FMT-exocortex-template | — | локальный |

## Ошибочная упрощённая модель

«Skill использует платформенный сервис → значит skill живёт в DS-IT-systems (репо платформы)». Из этого делается вывод о переносе skill'а в read-only DS-репо при первой же интеграции с Neon/Ory/GitHub.

## Корректная модель

Skill — артефакт пользователя (L3) или шаблона (L1), а не платформы. Skill всегда живёт в FMT-exocortex-template. Зависимость от платформенного состояния — это **маркер канала запуска**, не основание для переноса в DS-IT-systems.

## Тест

«Где локализовано постоянное состояние, на которое skill опирается?"
- Локально / Git → FMT, локальный канал.
- Neon / Ory / GitHub App → FMT + `platform_integration: true`, канал = Aisystant MCP / Cloud Gateway.
- Нет state → FMT, локальный канал.

## Применение

Решение задачи «куда положить skill»:
1. Определить **что skill читает/пишет в персистентном слое** (БД, OAuth, internal-GitHub-App).
2. Если все источники локальны (файл, git, переменные окружения) → FMT без маркеров.
3. Если хоть один источник платформенный → FMT + `platform_integration` в frontmatter SKILL.md + указать канал.
4. Если skill stateless → FMT, default.

## Антипаттерн

Skill `consent-management.md` использует Neon `learning.tracking_consent` → автор кладёт его в `DS-IT-systems/aisystant/`, обосновывая «он же про платформу». Результат: skill попадает в read-only-DS-репо → правка skill'а требует деплоя платформы, теряется L3 customization-loop.

## Связи

- DP.D.056 — IWE Слои и портируемость (общий контекст уровней).
- DP.D.073 — Storefront vs Internal Platform (родственная развилка «где живёт интерфейс»).
- AR.215 — Canonical remote org repo (источник правды для git-state).
