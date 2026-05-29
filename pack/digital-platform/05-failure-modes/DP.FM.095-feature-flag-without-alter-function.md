---
id: DP.FM.095
type: failure-mode
name: Feature-flag activated without ALTER FUNCTION
pack: PACK-digital-platform
domain: digital-platform
trust: high
epistemic_stage: validated
valid_from: 2026-05-28
source: session-transcript 2026-05-28, WP-327 v4.4 apply
---

# DP.FM.095 — Feature-flag activated without ALTER FUNCTION

## Описание

Флаг функциональности включён в конфигурации, однако фактическая SQL-функция в production базе данных осталась в старой версии. Система работает без ошибок, результат вычисляется неверно — расхождение не обнаруживается автоматически.

## Контекст возникновения

Спека утверждает «уже реализовано, активируется флагом». Разработчик включает флаг — считает миграцию готовой. Фактически SQL-функция в production не была обновлена через `ALTER FUNCTION`. Нет ошибок, нет логов — только неверный результат.

## Детектор

Перед утверждением «уже реализовано» — выполнить:

```sql
SELECT pg_get_functiondef(p.oid)
FROM pg_proc p
WHERE p.proname = '<имя_функции>';
```

Сравнить вывод с эталоном из текущей спеки. Расхождение → применить `ALTER FUNCTION` явно.

## Применимость

Любая миграция, которая «активирует» существующую SQL-функцию через флаг без явного `ALTER FUNCTION`. Особенно критично для reward/billing функций, где неверный результат не бросает исключений.

## Связи

- Урок: `memory/lessons_wp327_v44_apply.md`
