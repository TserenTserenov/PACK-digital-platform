---
id: DP.FM.129
title: "Broken symlink causes silent YAML config field loss with empty-list fallback"
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-02
source: Day Open Mode A, commit 719a12fb DS-my-strategy, WP-357 calendar pipeline
---

# DP.FM.129 — Broken symlink → silent YAML config field loss

## Паттерн отказа

Config YAML читается через символическую ссылку. Симлинк ломается. Парсер YAML читает пустой файл или файл без поля → обязательное поле становится `[]` вместо ошибки. Downstream-скрипт работает с пустым значением без сигнала об ошибке.

## Симптом

- Скрипт выполняется «успешно»
- Вывод содержит дефолтное/пустое значение: «(событий нет)», «0 результатов»
- Пользователь думает, что данных нет, а не что конфиг сломан

**Пример:** `remind-day-open.sh` шлёт Telegram-дайджест «(событий нет)» при `calendar_ids: []` из broken symlink `exocortex/day-rhythm-config.yaml`.

## Диагноз

```bash
ls -la path/to/symlink  # → broken arrow (красный)
cat path/to/symlink      # → cat: No such file or directory
```

## Фикс

Явная валидация обязательных полей при старте pipeline:

```bash
CALENDAR_IDS=$(yq '.calendar_ids[]' "$CONFIG_FILE" 2>/dev/null)
if [[ -z "$CALENDAR_IDS" ]]; then
    echo "ERROR: calendar_ids is empty in $CONFIG_FILE (broken symlink?)" >&2
    exit 1
fi
```

## Правило применения

Любой config-driven pipeline обязан валидировать обязательные поля при **старте** — поле пустое → exit 1, не silent fallback.

## Связи

- Аналог: DP.FM.100-stale-snapshot-silent-misdiagnosis.md (silent failure через stale data)
- Контекст: WP-357 calendar pipeline, `DS-my-strategy/exocortex/day-rhythm-config.yaml`
