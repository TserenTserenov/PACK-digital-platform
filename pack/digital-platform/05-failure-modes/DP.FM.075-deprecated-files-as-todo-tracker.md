---
id: DP.FM.075
name: deprecated-files-as-todo-tracker
type: fm
status: draft
pack: digital-platform
layer: L4-Platform
summary: "Запись артефакта в `deprecated_files` до удаления всех зависимостей в коде — превращает список устаревших в TODO-трекер, что вызывает runtime-drift при следующем update."
created: 2026-05-22
wp: WP-347
---

# [DP.FM.075] deprecated_files как TODO-трекер

## Симптом

Артефакт (промпт, скрипт, файл) добавлен в `deprecated_files` с комментарием «скоро мигрируем» или «будет удалено», но вызывающий код (`runner`, `dispatcher`, `setup.sh`) по-прежнему ссылается на него. После следующего `update.sh` / очистки — runtime падает.

## Контекст инцидента

**B11 (WP-347, 22 мая 2026):** 4 промпта `roles/strategist/prompts/` помечены в `deprecated_files` с reason `migrated to run_skill`, но `strategist.sh` вызывал `run_claude` через них. После `update.sh` runner упал бы.

## Корень

`deprecated_files` семантически означает «уже мигрировано, безопасно удалять» — не «планируем мигрировать». Когда запись идёт до удаления зависимостей, список теряет семантику и превращается в TODO.

## Правило (Конвенция И-10)

Добавлять артефакт в `deprecated_files` **только** если в том же коммите удалены ВСЕ зависимости в коде.

**До удаления зависимостей:** использовать `# TODO: migrate to run_skill` в коде или issue в трекере.

## Детектор

`integration-contract-validator.sh` Detector 10: статически проверяет пересечение `deprecated_files` и ссылок в runner-коде на CI. Детектор — щит после нарушения; конвенция И-10 предотвращает на этапе написания.

## Применимость

Любая manifest-driven система с `deprecated_files`, `legacy_entries`, `removed_in_next` или аналогичными списками устаревших артефактов.

## Связи

- **Детектор:** `integration-contract-validator.sh` Detector 10
- **Конвенция:** `И-10` в RELEASE-PROCESS.md
- **Смотри также:** DP.FM.010 (прыжок в реализацию без IntegrationGate), DP.M.010 (WP lifecycle)
