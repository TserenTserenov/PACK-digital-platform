---
id: DP.M.076
name: Migration flag (default WARN → opt-in FAIL) для постепенной валидации
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-322 Ф1.3 (DS-principles-curriculum/890b15b — v4-lint.py structure --strict-pack)
  - session-transcript 2026-05-17
related:
  complements: [DP.M.012]  # Machine-Check Postcondition (зачем валидатор); здесь — как ввести валидатор без ломки CI
  applies_to: [lint, type-check, security-scanner, Pack-coherence validator, schema-strict gates]
---

# DP.M.062: Migration flag для постепенной валидации (WARN → FAIL)

## Определение

Метод введения нового блокирующего правила (Pack-first, type-check, security-rule) поверх кодовой/контентной базы, где **большая доля legacy не соответствует**: дефолт — `WARN` (видно в логах, не блокирует), явный opt-in флаг — `FAIL` (новые PR пишутся с флагом, чтобы не регрессировать; легаси чистится постепенно). После завершения миграции дефолт меняется на `FAIL`, флаг убирается.

## Проблема

Big-bang перевод на `FAIL` ломает CI на месяц/квартал до завершения миграции. Альтернатива «уберём только то, что точно блокирует, добавим всё разом потом» на практике превращается в отдельный мегаспринт с риском, или «потом» не наступает.

## Триггер применимости

> «Вводимое правило валидно для будущего кода, но текущий код массово нарушает (≥10% violations)?»

- **Да** → migration flag.
- **Нет** (нарушений мало или нет) → сразу `FAIL`.

## Компоненты pattern'а

1. **Дефолт = WARN** — выводится в логи, не возвращает exit-code != 0.
2. **Opt-in флаг `--strict-X` = FAIL** — exit-code != 0, блокирует merge.
3. **Явная пометка в коде/доке** что это **миграционный** режим, не постоянный (комментарий рядом с флагом + ссылка на migration WP).
4. **Критерий снятия флага** — legacy-violation count → 0 (или ниже порога). После — `default := FAIL`, `--strict-X` удаляется.

## Применимость

- Lint-правила (eslint, ruff, golangci-lint).
- Type-checker'ы (mypy `--strict` per-file, pyright severity).
- Security-scanner severity (bandit, gitleaks).
- Pack-coherence валидаторы (v4-lint, Pack-first).
- Schema-strict CD-gates (OpenAPI, GraphQL, JSON Schema).

## Связи

- **Дополняет:** feedback_release_gates.md — там «валидатор без интеграции в pre-commit/CI = в чужих руках»; здесь — как интегрировать не ломая CI.
- **Дополняет:** DP.M.012 (Machine-Check Postcondition) — там «зачем валидатор», здесь «как ввести».
