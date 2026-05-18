---
id: DP.M.088
name: CI + pre-commit как defense-in-depth для Pack-инвариантов
name_en: CI + pre-commit defense-in-depth for Pack invariants
type: method
status: active
summary: "Двухуровневая защита Pack-инвариантов: pre-commit hook = быстрый локальный fail; GitHub Action = серверный enforcement при push/PR. Агентские коммиты (--no-verify, headless) покрываются только CI-слоем."
created: 2026-05-18
valid_from: 2026-05-18
trust:
  F: 3
  G: domain
  R: 0.85
epistemic_stage: evidence
related:
  see_also: [DP.M.033, DP.FM.027]
tags: [ci, github-actions, pre-commit, pack, invariants, defense-in-depth, id-collision]
wp: WP-272
---

# DP.M.088: CI + pre-commit как defense-in-depth для Pack-инвариантов

## Контекст

Pack-инварианты (уникальность ID, обязательный frontmatter, запрещённые диапазоны) нарушаются двумя путями:
1. Ручные коммиты → pre-commit hook ловит на месте
2. Агентские коммиты (peer-agent Kimi, headless dispatcher, `--no-verify`) → pre-commit обходится

## Метод: два независимых барьера

**Барьер 1 — pre-commit hook (локальный):**
- Запускается при каждом `git commit` в интерактивном режиме
- Быстрый fail: блокирует коммит с нарушением до попадания в репо
- Слепое пятно: `--no-verify`, headless git, peer-agent workflow

**Барьер 2 — GitHub Action (серверный):**
- Файл: `.github/workflows/pack-lint.yml` в Pack-репо
- Триггер: `push`, `pull_request` (любой ветки)
- Проверяет те же инварианты: R1 (frontmatter), R2 (ID формат), R3 (dir routing), R4 (уникальность ID)
- Покрывает ВСЕ пути коммита, включая агентские

## Тест применимости

«Может ли инвариант быть нарушен agent-commit'ом без `--no-verify`?»
- Да → необходим GitHub Action
- Нет (только интерактивные коммиты) → pre-commit достаточен

## Стоимость внедрения

Один файл `.github/workflows/pack-lint.yml` на Pack-репо (копипаст с параметрами репо).
При изменении pack-lint.sh обновлять Action-файлы во всех Pack-репо.

## Связи

- pack-lint.sh R4 (детектор ID-коллизий) — реализация инварианта
- DP.M.033 (matrix-CI) — ортогональный паттерн тестирования шаблонов
- DP.FM.027 — Railway autodeploy failure mode (аналог: пропущенный серверный gate)
