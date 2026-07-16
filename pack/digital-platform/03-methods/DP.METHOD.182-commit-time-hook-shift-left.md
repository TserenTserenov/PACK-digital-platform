---
id: DP.METHOD.182
name: "Commit-time pre-commit hook как точка shift-left enforcement инварианта знаний"
type: method
pack: PACK-digital-platform
domain: digital-platform / enforcement-architecture
kind: Method
status: active
created: 2026-07-14
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-13
sources:
  - "session-close 2026-07-13, WP-429 Ф5 (выбор точки запуска детектора противоречий)"
related:
  complements: [DP.M.088]
schema_version: 1
---

# DP.METHOD.182 — Commit-time hook как точка shift-left enforcement

## Определение

Принцип размещения детектора инварианта знаний в точке коммита, а не в фоновом демоне.
Shift-left: enforcement сдвигается к источнику изменения.

## IPO

- **Вход:** изменение файла (staged diff) в репо
- **Процесс:** pre-commit hook проверяет инвариант на staged файлах
- **Выход:** commit проходит (инвариант соблюдён) или блокируется с объяснением нарушения

## Преимущества commit-time точки

1. **Контекст:** diff показывает что именно изменилось — проверка по delta, не по всему корпусу
2. **Момент истины:** enforcement в точке внесения знания
3. **Fail-fast:** блокирует коммит немедленно, без планировщика
4. **Нет lifecycle:** hook — функция, не демон; нет состояния, нет heartbeat

## Граница применимости

**Commit-time hook уместен, когда проверка занимает секунды (не минуты).**

При expensive-проверке (embedding search, full-corpus scan) → дополнительный CI-слой
(см. DP.M.088 — defense-in-depth).

## Отличие от DP.M.088

| Вопрос | Метод |
|--------|-------|
| Как организовать два слоя защиты? | DP.M.088 (pre-commit + CI) |
| Почему commit-time лучше background daemon? | DP.METHOD.182 (этот) |

## Контекст

WP-429 Ф5: выбор точки запуска детектора противоречий. Альтернативы (фоновый демон,
server-side pre-receive hook) отклонены. Archgate PASS.
