---
id: DP.FM.474
type: failure-mode
status: active
created: "2026-09-18"
valid_from: "2026-09-18"
name: "RLS-политика падает на живом запросе при сравнении text-возврата session-функции с uuid-колонкой без явного каста"
name_ru: "RLS-политика падает на живом запросе при сравнении text-возврата session-функции с uuid-колонкой без явного каста"
name_en: "RLS policy fails on live query comparing a text-returning session helper against a uuid column without explicit cast"
summary: "RLS-выражение (USING/WITH CHECK) сравнивает text-результат session-helper функции с uuid-колонкой напрямую — Postgres роняет запрос ('operator does not exist: uuid = text'), но только в момент первого реального запроса через эту политику, не при применении DDL."
pack: PACK-digital-platform
domain: digital-platform / postgresql
schema_version: 1
trust: medium
epistemic_stage: observed
source: "git commit d904692 в neon-migrations, sandbox/2026-09-17-wp578-mentor-notes.sql"
extraction_report: "DS-my-strategy/inbox/extraction-reports/2026-09-21-inbox-check.md"
source_candidate: 1
source_capture: "DS-my-strategy/inbox/captures/2026-09.md:2445"
related:
  see_also: [DP.FM.219]
tags: [postgresql, rls, uuid, type-cast, security-definer-adjacent]
---

# DP.FM.474 — RLS роняет запрос на несовпадении text/uuid без явного каста

## Тезис и механизм ошибки

RLS-политика сравнивает возврат session-scoped helper-функции (`app.require_account_id()`, тип `text`) напрямую с колонкой типа `uuid` (`stream_reader.account_id`). Postgres не готов молча привести один тип к другому в этом контексте и роняет запрос ошибкой `operator does not exist: uuid = text`. Ошибка не проявляется при `CREATE POLICY`/применении миграции — только когда первый реальный запрос проходит через эту политику в рантайме.

Тот же по форме каст (`::uuid`) уже был применён ранее в другом файле той же кодовой базы (`f1-storage.md:189`) — здесь пропущен повторно. Это указывает на пробел чек-листа/линтера для новых RLS-политик, использующих этот session-helper, а не на разовую невнимательность автора.

## Диагностический принцип

Несовпадение типов в RLS-выражении не ловится на этапе применения DDL: политика создаётся успешно, и отказ откладывается до первого живого запроса. Поэтому проверка миграции без реального запроса через политику не подтверждает её работоспособность.

## Границы

Применимо к RLS-выражениям (`USING`/`WITH CHECK`), где результат session-helper функции сравнивается с колонкой, чей тип не совпадает буквально с типом возврата функции. Если типы совпадают буквально, каст не нужен.

## Проверка

«RLS-выражение (`USING`/`WITH CHECK`) сравнивает возврат session-helper функции напрямую с колонкой, чей тип не совпадает буквально с типом возврата функции?» Да → риск: ошибка не будет поймана на этапе применения DDL, только на первом реальном запросе, прошедшем через политику.

Контрмера: явный `::uuid`-каст (или приведение типов на уровне определения helper-функции) в каждом RLS-выражении, вызывающем этот session-helper. При написании новой RLS-политики на uuid-ключевой таблице сверяться с уже решёнными прецедентами каста в кодовой базе (`f1-storage.md:189`), не полагаться на память.

## Происхождение и статус проверки

Источник: git commit `d904692` в `neon-migrations`, `sandbox/2026-09-17-wp578-mentor-notes.sql`. Захват: `DS-my-strategy/inbox/captures/2026-09.md:2445`. Связь: WP-578 Ф3.

Смежно, не дубль: `DP.FM.219` (приведение типа в WHERE на большой таблице рушит index scan) — смежный тип-каст паттерн Postgres, другой отказ: там тихая деградация производительности (seq scan вместо index scan), здесь громкая ошибка запроса (`operator does not exist`). Общий корень (небрежность на границе uuid/text), разные проявления.

Описанная проверка задаёт критерий приёмки. Её прохождение исходной реализацией этой карточкой не утверждается.
