---
id: DP.M.055
name: Config SoT Triplet (Python source + SQL generator + validator)
type: method
status: active
trust: 0.7
epistemic_stage: emerging
evidence_strength: single-precedent
evidence:
  - WP-319 Ф3.5 reward_config triplet (activity-hub commit 779be3f, 2026-05-16)
domains: [config-management, sot, drift-prevention]
created: 2026-05-16
last_updated: 2026-05-16
valid_from: 2026-05-16
---

# DP.M.055 — Config SoT Triplet

## Обещание

Константы, которые читаются и кодом, и SQL-запросами (Metabase, dashboards, прямые запросы из DB), не расходятся между источниками. Drift детектируется детерминированно в CI/pre-commit, не в продакшене.

## Inputs

- Набор констант, дублирующийся между application code и database table
- CI или pre-commit pipeline

## Outputs

- **Source-of-truth файл** (например `core/<feature>_config.py`) — единственное место правки
- **Генератор SQL-патча** (`scripts/generate_<feature>_sql.py`) — производит DDL/DML из SoT
- **Validator-скрипт** (`scripts/validate_<feature>_config.py`) — exit 1 при drift

## Алгоритм (триплет)

1. **Python-файл = SoT** с явной `CONFIG_VERSION`. Все правки констант — только здесь.
2. **Генератор** читает SoT, выдаёт SQL-патч для целевых таблиц. Запускается при изменении SoT.
3. **Validator** в CI/pre-commit: загружает SoT, сравнивает с актуальным состоянием DB-таблиц. Расхождение → exit 1.

## Roles

- **Owner:** разработчик SoT-файла
- **Consumer:** CI pipeline (validator), DB migration runner (generator output)

## Когда применять

- Константы читаются ≥2 разными технологиями (Python код + SQL запросы Metabase)
- Изменения происходят итеративно, миграции громоздкие для каждой правки
- Drift между кодом и DB исторически приводил к ошибкам

## Когда НЕ применять

- Константы читаются только из кода (тогда обычная переменная)
- DB-таблица сама по себе SoT (нет дублирования)
- Изменения редкие (раз в полгода) — миграция дешевле триплета

## Альтернативы

- **«Таблица DB как SoT»:** проигрывает, потому что Python нельзя инспектировать без живого подключения, версионирование через миграции громоздкое для итеративных правок.
- **Hard-code в обоих местах:** drift гарантирован.

## Failure modes

- Generator не запущен после правки SoT → validator поймает в CI
- Validator не подключён в pipeline → drift не детектируется

## Evidence

- WP-319 Ф3.5 (activity-hub 779be3f, 2026-05-16): reward_config (BASE_AMOUNTS, DOMAIN_MULT, STAGE_MULT) — Python SoT + SQL-генератор + validator. ArchGate Вариант А.
