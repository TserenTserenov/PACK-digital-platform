---
id: DP.FM.032
name: Repair-Pass Stale-Hash Blind Spot (Слепое пятно устаревшего файла при repair-pass)
category: deployment
severity: major
status: draft
summary: "Repair-pass проверяет только отсутствие файла (! -f), но не его актуальность (hash vs source). Если файл существует, но содержимое расходится с FMT-source, он остаётся без обновления. Silent stale-регрессия."
created: 2026-05-13
valid_from: 2026-05-13
related:
  extends: [DP.FM.007]
  see_also: [DP.FM.009]
tags: [deployment, update, hash, sync, template, repair]
source: "git diff FMT-exocortex-template commit e7ebf71, WP-7, update до 0.30.0"
---

# [DP.FM.032] Repair-Pass Stale-Hash Blind Spot

## Суть паттерна

При обновлении через `update.sh` repair-pass восстанавливает **только отсутствующие файлы** (`if [ ! -f "$dst" ]`). Если файл существует, но его содержимое устарело (hash расходится с source в FMT), он **остаётся без изменений** — без ошибки, без предупреждения.

Статус «UNCHANGED» означает «удалённый источник не изменился», но **не гарантирует актуальность локального файла**.

## Механизм

1. **Partial apply предыдущего update** — update завершился с ошибкой на полпути, часть файлов не записана.
2. **Dirty workspace** — файл был изменён вручную или конфликтом merge.
3. **Ручное редактирование** — пользователь отредактировал платформенный файл, не зная что он будет перезаписан.

В каждом случае `[ ! -f "$dst" ]` возвращает false → repair-pass пропускает файл → stale-содержимое остаётся.

## Защита

**Правило:** repair-pass должен проверять **два условия**:
1. файл существует (`[ -f "$dst" ]`)
2. hash совпадает с source (`sha256sum "$src" == sha256sum "$dst"`)

```bash
# Правильная проверка в repair-pass:
if [ ! -f "$dst" ] || ! cmp -s "$src" "$dst"; then
    cp "$src" "$dst"
    echo "REPAIRED: $dst"
fi
```

**Альтернатива:** принудительный re-apply всех файлов в repair-mode — безопаснее, медленнее.

## Связь с другими FM

- **DP.FM.007** (View Drift): оба — рассинхронизация артефактов без явной ошибки. FM.007 = view-model drift, FM.032 = source-target drift в delivery.
- **DP.FM.009** (Protocol Hardcoded Script Path): оба — инфраструктурные ошибки в update/deploy pipeline.
