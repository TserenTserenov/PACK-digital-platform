---
id: DP.FM.132
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: validated
epistemic_stage: 3
valid_from: 2026-06-05
source: WP-392 Б1 (session-close 2026-06-05, commit c7cd36740)
---

# DP.FM.132 — Microservice Tier SoT Mismatch

**Failure mode:** Бизнес-параметр X (tier, subscription status, access level) хранится в БД сервиса A и используется сервисом B из собственной БД без механизма синхронизации → B видит устаревший default.

## Симптом

Пользователь с X=T4 у сервиса A отображается как X=T2 у сервиса B. Новая функциональность недоступна. Диагноз затруднён: обе БД работают, никаких ошибок нет.

## Механизм

Microservice A хранит бизнес-параметр X в своей таблице Y. Microservice B читает тот же X из своей таблицы Z. Колонка Z.X не была добавлена миграцией → B всегда видит SQL-default значение.

**Класс:** рассогласование source-of-truth для shared business contract.

## Тест

«Есть ли единственное место, где X меняется?» — Нет → mismatch неизбежен при рассинхроне схем.

## Решения (по возрастанию coupling)

1. **Shared БД как единый SoT** — оба сервиса читают одну таблицу (если изоляция не критична).
2. **Bridge-сервис** — читает основной источник, обновляет реплику.
3. **Event bus** — сервис A публикует событие при изменении X, B подписывается и обновляет свою БД.
4. **Direct cross-service read** — B вызывает API A при каждом запросе (latency + coupling).

**Антипаттерн:** ручное дублирование через manual SQL — drift гарантирован.

## Правило

Бизнес-параметр с межсервисным контрактом обязан иметь: явный SoT + механизм синхронизации, зафиксированный в архитектурном решении ДО реализации второго потребителя.

## Источники

- WP-392 Б1 (session 2026-06-05): tier бота vs tier шлюза разъединены
- Related: `DP.D.060` (entity DB vs special DB), OwnerIntegrity principle
