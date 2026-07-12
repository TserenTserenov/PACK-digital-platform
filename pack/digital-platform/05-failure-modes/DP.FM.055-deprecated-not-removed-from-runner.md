---
id: DP.FM.055
type: failure-mode
name: "deprecated_files в manifest ≠ удалён из runtime-runner"
domain: template-lifecycle
epistemic_stage: empirical
trust: verified
valid_from: 2026-05-20
source: "FMT-exocortex-template commit e337183"
---

# DP.FM.055 — deprecated_files в manifest ≠ удалён из runtime-runner

## Симптом

Файл помечен в `deprecated_files` секции `update-manifest.json`, но продолжает
исполняться в runtime-runner (hook, скрипт, cron).

## Механизм

`update-manifest.json:deprecated_files` — декларативный список для `update.sh`.
`update.sh` использует список для удаления у пользователей при обновлении шаблона.
Но runtime-runner (хук или cron) читает файл напрямую из файловой системы.
Если файл физически не удалён из репозитория — он продолжает выполняться.

Два независимых механизма: manifest (для обновления у пользователей) и fs (для runtime).
Запись в manifest ≠ удаление из fs.

## Условия возникновения

- Автор шаблона добавил файл в `deprecated_files`, но не удалил физически.
- Тест: запустить `ls <path>` — файл существует → FM активен.

## Последствия

- Устаревший хук продолжает выполняться после deprecation.
- Пользователи после `update.sh` не имеют файла, автор — имеет.
- Divergence между авторской средой и пользовательскими.

## Детектор

```bash
# Проверить deprecated_files manifest vs filesystem
python3 -c "
import json, os
m = json.load(open('update-manifest.json'))
for f in m.get('deprecated_files', []):
    if os.path.exists(f):
        print(f'STILL EXISTS: {f}')
"
```

## Фикс

Удалить файл физически (`git rm`) + commit. Только потом — в deprecated_files manifest.
Порядок: physical removal first, manifest annotation second.

## Источник

FMT-exocortex-template, commit e337183. Обнаружено при аудите runtime-runner.

## Связи

- [DP.FM.075](DP.FM.075-deprecated-files-as-todo-tracker.md) — смежный failure mode того же механизма `deprecated_files`, но другой момент сбоя: этот файл — запись в manifest БЕЗ физического удаления; DP.FM.075 — запись в manifest ДО удаления зависимостей в вызывающем коде. Разные классы ошибок, не дубли.
