---
id: DP.D.180
name: "Сгенерированный файл ≠ Живой файл — машиночитаемый критерий"
type: distinction
domain: digital-platform
pack_refs: [DP.D.049]
status: active
valid_from: 2026-06-04
schema_version: 1
source: "current/docs audit Claude + Kimi (2026-06-04), git diff DS-my-strategy (79d0458ba)"
---

# DP.D.180 — Сгенерированный файл ≠ Живой файл

## Различение

**Сгенерированный (derived):** файл, который воспроизводится скриптом/агентом при каждом запуске и не должен коммититься.

**Живой (authored):** файл, содержащий ручные решения и изменяющийся только через явное редактирование.

## Машиночитаемый критерий

| Признак | Где искать | Вывод |
|---------|-----------|-------|
| `generated_at:` в YAML-frontmatter | первые строки файла | → derived, в .gitignore |
| `# AUTO-GENERATED` / `# DO NOT EDIT` | первые строки файла | → derived, в .gitignore |
| Ни одного из выше | — | → authored, можно коммитить |

## Применение

- При создании нового генератора/скрипта: добавлять маркер в output
- При code review: проверять .gitignore для новых файлов с этими маркерами
- При аудите репо: `grep -r "generated_at:" . | grep -v ".gitignore"` — кандидаты в .gitignore

## Связь с DP.D.049

DP.D.049 «Лог ≠ Инцидент ≠ State file» определяет семантику state-файлов.
DP.D.180 добавляет операциональный критерий: как отличить state-файл от authored-файла **автоматически**.

## Ошибочная модель

«Файл в `current/` значит generated» — нет. Критерий — маркер, не директория. В `current/` могут лежать authored файлы (DayPlan). В других директориях — generated (date-ledger.yaml).
