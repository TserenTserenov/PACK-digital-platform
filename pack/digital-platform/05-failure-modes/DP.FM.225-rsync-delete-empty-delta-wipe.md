---
id: DP.FM.225
name: "rsync --delete с пустой delta-staging директорией уничтожает все артефакты"
type: fm
pack: PACK-digital-platform
domain: digital-platform / ci-pipeline
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-06, FMT-exocortex-template translate-sync CI (commit 791df86)"
schema_version: 1
---

# DP.FM.225 — rsync --delete с пустой delta-staging уничтожает все артефакты

**Суть:** Delta pipeline записывает только изменённые файлы в staging dir. `rsync --delete` с пустым источником интерпретирует «пусто = всё удалено» → удаляет ВСЕ файлы в целевой директории.

## Механизм

1. CI-пайплайн пишет дельту в `../staging-out/`
2. `rsync --delete ../staging-out/ ../dest/`
3. При нулевой дельте `../staging-out/` пуст
4. `rsync --delete` видит пустой источник → удаляет все файлы в `../dest/`

## Реальный инцидент

Translate-sync CI (2026-07-06): нулевой прогон удалил все 38 ранее переведённых документов из en-draft.

## Тест

«Что произойдёт при нулевой дельте?» Если ответ — «удалятся все предыдущие файлы» → ошибка дизайна.

## Фикс

Писать дельту напрямую в постоянный checkout, где предыдущие файлы уже лежат — не в throwaway staging. `rsync --delete` тогда видит только реально изменившиеся файлы.
