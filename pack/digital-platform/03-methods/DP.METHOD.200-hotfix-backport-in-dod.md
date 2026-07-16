---
id: DP.METHOD.200
name: "Backport в upstream-ветку как критерий закрытия хотфикса"
type: method
status: active
valid_from: 2026-07-10
source: "WP-7 PRODSYNC2; peer-session 2026-07-09-18-prodsync-branch-divergence; git commit d50468416"
summary: "Хотфикс считается закрытым только после backport в upstream/staging-ветку + verify-скрипт перед каждой волной доставки."
tags: [hotfix, backport, branch-discipline, release-management, definition-of-done]
---

# DP.METHOD.200 — Backport в upstream-ветку как критерий закрытия хотфикса

## §0 Проблема

При pilot-first дисциплине (pilot → prod) хотфиксы, применённые прямо на прод-ветку без обратного переноса, накапливаются молча. За 3 недели: 100+ прод-only коммитов, несколько миграций без эквивалента в pilot. Следующая волна pilot→prod рискует откатить прод или упасть на несовпадении схем БД.

## §1 Правила

1. **Backport = часть definition of done хотфикса.** Хотфикс не закрыт, пока не бэкпортирован в upstream/staging-ветку. Закрытие = [hotfix merged to prod] AND [backport to pilot] AND [pilot CI green].

2. **Verify-скрипт перед каждой волной доставки.** Перед pull pilot→prod запускать скрипт, сверяющий staging-БД/ветку с прод-фактом: миграции, схема. Предположение «ветки эквивалентны» без проверки — источник data-loss.

## §2 Алгоритм

```
[hotfix merged to prod]
       ↓
[backport PR to pilot/staging]
       ↓
[pilot CI green]  →  [mark hotfix DONE]

[pre-wave check] → verify-script: pilot-DB vs prod-fact
   OK      → proceed
   MISMATCH → reconcile first, then wave
```

## §3 Инварианты

- Ни один хотфикс не закрывается без backport в upstream-ветку
- Verify-скрипт блокирует волну при расхождении (не advisory)
- Список миграций в pilot — superset prod-миграций или идентичен

## §4 Связи

- pilot-first дисциплина: `MEMORY.md` (бот: деплой, Pilot-First)
- DP.FM.270 (bootstrap-migration-drift-multi-version): последствия накопленного дрейфа миграций
