---
id: DP.M.107
title: Role rename = ритуал пересмотра downstream_consumers
type: method
domain: digital-platform
status: active
tags: [pack-engineering, role, rename, governance, downstream]
valid_from: 2026-05-20
schema_version: 1
---

# DP.M.107 — Role rename = ритуал пересмотра downstream_consumers

## Описание

Переименование роли (DP.ROLE.*, MIM.R.*) ≠ изменение строки `title:`. Rename — повод пересмотреть `downstream_consumers`, потому что новое имя часто выявляет упущенных потребителей. Старое имя могло скрывать универсальность роли через product-specific терминологию; новое — обнажает.

## Вход

Намерение переименовать роль (запрос на rebrand, обнаруженный drift между title и actual scope, унификация терминологии)

## Выход

- Обновлённый `title:` + `previously_named: «X (до YYYY-MM-DD)»` в frontmatter
- Пересмотренный `downstream_consumers:` (часто расширенный)
- Explicit `not_consumers:` для предотвращения путаницы с близкими ролями
- `specializes: <parent-role>` если выявилась родительская роль

## Алгоритм

1. Sweep всех файлов Pack, упоминающих старое имя роли → grep по `<old-title>`, `<old-id>`
2. Sweep downstream-репо (DS-*) и memory/ — где роль упоминается?
3. Для каждого найденного места: это consumer? consumer но другой роли (rename выявил смешение)? not-consumer (явная не-зависимость)?
4. Сформировать список consumers + not_consumers + (опционально) parent-role
5. Обновить frontmatter роли: title, previously_named, downstream_consumers, not_consumers, specializes
6. Закоммитить atomic-rename (см. DP.M.095 cross-repo-terminology-sync)

## Критерий применимости

«Меняем `title` роли в Pack?» Да → ритуал обязателен. Запрещено менять title без шагов 1-5.

## Failure mode без метода

Rename как cosmetic-fix → старое имя продолжает использоваться в downstream → drift. Через 3-6 месяцев accumulating drift делает rename бесполезным (грузим оба имени, никто не помнит почему).

## Прецедент

WP-326 (2026-05-20, commit afb2139): DP.ROLE.046 «IWE Stage Controller» → «Контролёр развития». `downstream_consumers` расширен с 2 до 5 (+ Навигатор + Проводник + Диагност-напоминание). `not_consumers: Аттестатор` (он реактивный, не дублирует). `specializes: DP.ROLE.022 Оркестратор`.

## Связи

- **DP.M.095** (atomic-cross-repo-terminology-sync) — операционный аспект (как раскатить rename через N репо)
- **DP.METHOD.030** (term-translation) — про межсистемный перевод понятий (другой scope)
