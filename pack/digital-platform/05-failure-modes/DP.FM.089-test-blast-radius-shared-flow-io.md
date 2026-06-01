---
id: DP.FM.089
type: failure-mode
title: Test blast-radius при добавлении I/O в shared flow
status: proposed
trust: provisional
epistemic_stage: B
created: 2026-05-27
source: session-transcript 2026-05-27 + commit b9c14201 aist_bot_newarchitecture
domains: [testing, mocking, shared-flow, ci]
---

# DP.FM.089 Test blast-radius при добавлении I/O в shared flow

## Симптом

После добавления нового I/O-вызова (БД, HTTP, файл) внутри функции, которую вызывают N тестов с mock-credentials, все N тестов падают с auth/connection error. Класс ошибки выглядит как infra/credentials, маскируя реальную причину (свежий неpatched вызов).

## Прецедент

2026-05-27, commit b9c14201 (`aist_bot_newarchitecture`):

- Добавили `clear_marathon_state` в `start_marathon_flow`.
- Два теста caller'ов упали с `asyncpg.InvalidAuthorizationSpecificationError: role "fake" does not exist`.
- Диагноз: функция пыталась подключиться к реальному пулу с fake credentials, потому что тесты не патчили новый вызов.
- Fix: `patch handlers.marathon.clear_marathon_state` в обоих тестах.

## Корень

Изменение shared-функции расширяет её I/O-поверхность, но caller'ские mock-сценарии этого не учитывают — каскадный сбой не виден на этапе ревью diff'а целевой функции.

## Анти-паттерн

Чинить только новый тест целевой функции, забывая каскадные тесты caller'ов. → flaky CI, ложный диагноз "infra problem", повторное расследование при следующем прогоне.

## Митигация (pre-commit checklist)

При модификации shared-функции, добавляющей новую I/O-зависимость:

1. `grep -r "<func_name>("` — найти всех caller'ов.
2. Для каждого теста caller'а — добавить mock/patch на новую I/O-зависимость.
3. Прогнать полный test-suite caller'ов локально.
4. Только потом commit.

## Контекст применимости

Python pytest+monkeypatch/unittest.mock, JS jest, Go testify — везде, где тесты caller'а используют mock'и для внешних систем. Особенно острая проблема в integration-тестах с fake-credentials, где новый I/O = немедленный ConnectionError.

## Связи

- **Прецедент:** commit b9c14201 (`aist_bot_newarchitecture`)
- **Смежные FM:** DP.FM.043 (case-enum-assumption), DP.FM.046 (render-queue-callsite-timeout) — другие классы blast-radius
