---
id: DP.M.209
name: "Dry-run = 50% production migration: полный checklist с явным блокером"
type: method
domain: data-migration / deployment / pre-deadline-preparation
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-close 2026-05-28 (peer-session 14: WP-327 backfill, deadline 2026-06-01)"
---

# DP.M.209 Dry-run = 50% production migration: полный checklist с явным блокером

## Решение

При подготовке data migration / backfill / ETL replay-job к фиксированному дедлайну считать dry-run половиной работы. Финальный артефакт (markdown / runbook / Notion-страница) обязан содержать полный production-run раздел с явным списком блокеров — иначе день D приедет «неожиданно» и человек откроет документ с одним dry-run-блоком, не сможет довести миграцию до конца без на ходу собранных команд.

## Обязательная структура checklist

1. **Dry-run SQL с verifiable output.** Команда + ожидаемый вывод + что сверить (row count, sample row).
2. **Sanity checks ≥3.** Cardinality (сколько затронуто), NULL distribution (нет ли регрессии), edge cases (boundary-условия). Без минимум 3 проверок dry-run не считается завершённым.
3. **Production run инструкция.** Точная команда (или последовательность), окружение (prod-db, prod-cluster, branch), оператор (кто запускает, on-call), ожидаемая длительность.
4. **Verify SQL после прогона.** Что проверить после успешного запуска: row count совпал с dry-run? агрегаты совпадают с прогнозом? даунстрим (projections, materialized views) обновился?
5. **Явный список блокеров с named credentials/permissions.** Не «нужны права», а `NEON_API_KEY`, `prod write access`, `on-call coverage 06:00-09:00 МСК`, `consent freeze в окне миграции`. Каждый блокер с владельцем и способом получения.

## Аргумент

- **Симметрия времени:** dry-run и production-run обычно занимают сопоставимое время. Если в документе только dry-run — половина артефакта отсутствует.
- **Дедлайн = единственная гарантия запуска.** В день D на ходу собирать команды = повышенный риск опечатки в `WHERE`, забытого `BEGIN` без `COMMIT`, неправильного env-var. Каждая неподготовленная строка — отдельный шанс отката.
- **Блокеры приходят не от автора.** `NEON_API_KEY` лежит у DevOps, on-call — у партнёра, consent freeze — у продакта. Без явного списка автор узнаёт о блокере утром день-D.

## Тест полноты

«Если завтра автор недоступен, может ли on-call выполнить миграцию по этому документу без вопросов?»
- Да → checklist полный
- Нет (есть пропуск или предположение «понятно же») → не полный, нужно достроить

## Граница применимости

- Data migrations с фиксированным cut-over moment
- Backfills с deadline (compliance, релиз-привязка)
- ETL replay-jobs одноразового действия
- Schema migrations с downtime window

## Анти-применение

- Идемпотентный backfill, который можно гонять много раз без последствий — production-run превращается в рутину, full checklist избыточен
- Continuous deployment миграции через автоматический раннер (Flyway / Liquibase / Atlas) — checklist не для людей, а для инструмента

## Анти-паттерн

Коммитить «backfill готов» при наличии только dry-run-блока — обещание шире реализации.

## Связи

- Источник: WP-327 backfill production prep, 2026-05-28
