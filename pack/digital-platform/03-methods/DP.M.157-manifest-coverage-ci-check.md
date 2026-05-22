---
id: DP.M.157
name: "CI-чек покрытия манифеста дистрибутива"
type: method
pack: PACK-digital-platform
domain: template-distribution
trust: 0.9
epistemic_stage: established
valid_from: 2026-05-22
pack_refs:
  - source: DP.IWE.001
    relation: applies-to
---

# DP.M.157 — CI-чек покрытия манифеста дистрибутива

## Суть

Метод предотвращения «скрытого выпадения» файлов из поставки template-системы. Новый файл добавлен в репо, но не зарегистрирован в update-manifest — пользователь не получает файл при обновлении.

## Failure mode

Разработчик добавляет `scripts/new-tool.sh` → коммитит → делает release. Пользователи, обновляющиеся через `update.sh`, не получают файл. Обнаруживается только когда кто-то замечает отсутствие.

## IPO

| | |
|---|---|
| **Вход** | Репо с явным манифестом файлов поставки; список директорий, подлежащих регистрации |
| **Процесс** | CI-скрипт обходит директории → сверяет с манифестом → список незарегистрированных файлов |
| **Выход** | CI падает с перечнем файлов в репо, отсутствующих в манифесте |

## Шаги

1. Определить «директории поставки» (scripts/, roles/, rules/, skills/ и т.п.)
2. Написать `check-manifest-coverage.py`: `set(files_in_dirs) - set(files_in_manifest)` → non-empty = failure
3. Добавить шаг в CI pipeline (GitHub Actions / аналог)
4. При первом запуске ожидать backlog — добавить незарегистрированные файлы за один коммит

## Тест применимости

«Может ли файл существовать в репо, но не доставляться пользователям?» Да → нужен manifest coverage check.

## Применимость

Любая template-система с явным манифестом: Homebrew formula, npm `files` в package.json, Python MANIFEST.in, IWE update-manifest.json, Ansible roles distribution.

## Связи

- Применено: FMT-exocortex-template commit 95817fa (WP-347 Ф2), обнаружено 19 незарегистрированных файлов
- Аналог: DP.M.087 (secrets-map pre-deploy CI gate)
