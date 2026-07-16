---
id: DP.FM.280
title: "Staging-таблицы cross-DB sync должны существовать на обоих концах"
type: FM
domain: digital-platform
status: candidate
trust: low
epistemic_stage: candidate
valid_from: 2026-07-13
source: "session-close 2026-07-09, git commit acefcc1 (payment-registry INCIDENT-2026-07-08)"
tags: [cross-db-sync, postgresql, bash-scripting, data-pipeline]
---

# [DP.FM.280] Staging-таблицы cross-DB sync должны существовать на обоих концах

## Описание failure mode

Скрипт cross-DB синхронизации предполагает наличие staging-таблиц на **целевой** стороне (target), но они созданы только на **исходной** стороне (source). Скрипт завершается без ошибки, данные не переносятся — особенно если дополнительно отсутствует `ON_ERROR_STOP=1` (psql heredoc без флага).

## Симптом

- Sync-скрипт логирует «done» / завершается exit 0
- Данные на target не появляются
- Ручная проверка `\dt staging.*` на target — пусто

## Причина

DDL-полнота схемы проверяется только на source при первоначальной настройке. Staging/temporary infrastructure не входит в стандартные migration-чеклисты — только конечные таблицы.

## Детектор

Перед первым прогоном sync выполнить на target:

```sql
SELECT tablename FROM pg_tables WHERE schemaname = 'staging';
```

Если пусто — таблицы не созданы.

## Fix

Создать идентичные DDL staging-таблиц на target. Добавить в deploy-чеклист: проверить `\dt staging.*` на target ДО первого прогона sync.

## Инвариант

При настройке cross-DB sync проверять DDL-полноту схемы на **обоих** концах, включая staging/temporary infrastructure, а не только конечные таблицы.

## Связи

- Инцидент: payment-registry INCIDENT-2026-07-08 (2026-07-09)
- Смежный failure: [DP.FM.274] psql без ON_ERROR_STOP скрывал ошибку при INSERT в несуществующую таблицу
