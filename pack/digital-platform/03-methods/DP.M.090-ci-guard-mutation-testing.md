---
id: DP.M.090
name: "Mutation Testing для CI Enforcement Guards в Pack-репо"
type: method
status: draft
created: 2026-05-18
valid_from: 2026-05-18
sources:
  - PACK-MIM commits 6b5da5f (smoke test) + 7f40893 (revert, CI fail confirmed)
related:
  applies_to: []
  references: [DP.D.048]
  complements: [DP.M.088]
---

# DP.M.090 — Mutation Testing для CI Enforcement Guards в Pack-репо

> Guard без проверки — не guard, а иллюзия безопасности.

## Обещание

После добавления нового CI guard (детектор дублирующихся ID, онтологический инвариант, схема-валидатор) убедиться, что guard реально срабатывает, а не молча пропускает нарушения (false-green).

## Вход

- Новый CI guard уже добавлен в pipeline (pack-lint.sh или GitHub Action)
- Доступ к целевому Pack-репо

## Выход

- Подтверждение: CI упал на намеренном нарушении (статус "CI fail confirmed")
- Коммит-revert с удалением smoke-нарушения

## Алгоритм (3 шага, 2 коммита)

1. **Создать намеренное нарушение инварианта** — добавить файл/секцию, которая должна нарушать guard (например, файл с дублирующимся ID)
2. **Push → убедиться, что CI упал** — ожидаемый статус: ❌ (CI fail confirmed)
3. **Revert + коммит** — удалить smoke-нарушение; сообщение коммита: `revert: remove smoke duplicate (CI fail confirmed)`

## Стоимость

2 коммита, ~5 минут.

## Failure mode без проверки

CI Action создан, но regexp/glob неверный → реальные коллизии/нарушения молча проходят → guard даёт ложную безопасность. Failure накапливается незаметно.

## Применимость

- При добавлении нового R{N} критерия в pack-lint.sh
- При добавлении нового GitHub Action для Pack-инвариантов
- При любом новом CI check, завязанном на структуру файлов Pack

## Связь с defense-in-depth

Дополняет DP.M.088 (CI + pre-commit = два барьера): тот объясняет зачем два барьера, этот — как убедиться что каждый барьер реально работает.
