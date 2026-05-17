---
id: DP.M.047
name: "Стресс-тест бэкапа через restore"
type: method
status: active
valid_from: 2026-05-15
source: "WP-session-close 2026-05-15; backup validation via restore pattern"
related:
  - DP.M.019
---

# DP.M.047 — Стресс-тест бэкапа через restore

## Проблема

Бэкап без проверки restore — не бэкап. Файл существует, но нет гарантии что данные восстановимы. «Мы делаем бэкапы» ≠ «мы умеем восстанавливаться из бэкапа».

## Паттерн

Бэкап считается валидным только после успешного restore-теста. Restore-тест = единственный способ проверить бэкап.

## Шаги

1. **Создать бэкап** стандартным методом (pg_dump, S3 snapshot, git archive)
2. **Restore в изолированную среду** — не в production, не в staging production
3. **Верифицировать данные** — N контрольных запросов / checksum / row count comparison
4. **Зафиксировать результат** — restore_ok: true/false + timestamp + ревизор

## Инварианты

- Restore выполняется в отдельную БД/namespace/директорию, не поверх prod
- Верификация — не «файл скачался», а «данные читаются корректно»
- Restore-test периодичность = не реже периодичности бэкапа

## Применимость

- Neon PostgreSQL (pg_dump → restore в dev-БД)
- Git-репозитории (clone + integrity check)
- S3/object storage (download + hash verification)
- Любая система с RPO > 0

## Failure modes

- Restore без верификации → бэкап «зелёный», но данные повреждены
- Restore в prod → двойная потеря (prod данных + restore-файла)
- Restore без изоляции → side effects на смежные системы

## Источник

Паттерн из feedback по backup-стратегии: без restore-теста обнаружение повреждения происходит в момент реального восстановления после инцидента.
