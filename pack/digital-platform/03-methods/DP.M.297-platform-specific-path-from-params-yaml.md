---
id: DP.M.297
title: "Платформо-специфичный путь: приоритет env > params.yaml > built-in fallback"
type: method
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
source: "git diff FMT-exocortex-template (1d65a90, roles/extractor/scripts/extractor.sh)"
valid_from: 2026-06-08
---

# Платформо-специфичный путь: env > params.yaml > built-in fallback

## Суть

Скрипты, вызывающие платформо-специфичные команды (osascript на macOS, notify-send на Linux), загружают путь через трёхуровневый приоритет:
1. Переменная окружения (`NOTIFY_SH_PATH`)
2. params.yaml (`notify_sh_path` поле)
3. Встроенный fallback (захардкоженный путь)

## Реализация (shell)

```bash
if [ -z "$NOTIFY_SH_PATH" ]; then
  NOTIFY_SH_PATH=$(grep "^notify_sh_path:" "$IWE_WORKSPACE/params.yaml" | sed 's/.*: *//')
fi
# fallback если params.yaml тоже пустой
NOTIFY_SH_PATH="${NOTIFY_SH_PATH:-/usr/local/bin/notify.sh}"
```

## Применимость

Любой IWE-скрипт с платформо-зависимыми путями: TTS, браузер, editor, системные уведомления. Условие: `params.yaml` доступен по `$IWE_WORKSPACE/params.yaml`.

## Обобщение

Паттерн «env-first, config-second, default-last» — стандарт 12-factor для платформо-специфичного конфигурирования. Не хардкодить платформенные пути в скрипты.

## Связи

- params.yaml: `IWE_WORKSPACE/params.yaml` (авторское расширение)
- Применено в: `roles/extractor/scripts/extractor.sh` (issue #17)
