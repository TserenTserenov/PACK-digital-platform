---
id: DP.FM.029
name: Cross-Platform Path Leak (Утечка платформо-специфичных путей)
category: infrastructure
severity: major
status: active
summary: "В конфигурации или коде кросс-платформенного инструмента прописан платформо-специфичный путь (macOS /Users/... slug, Windows C:\\...). На целевой платформе (Linux/сервер) путь не существует, инструмент молча выдаёт WARN и продолжает работу — без явной ошибки."
created: 2026-05-12
valid_from: 2026-05-12
related:
  see_also: [DP.FM.009]
tags: [infra, cross-platform, path, silent-fail, template, smoke-test]
source: "template-sync.sh MEMORY_SRC macOS slug на tsekh-1 (Linux), WP-5/WP-7 Stability-4, 12 мая 2026"
---

# [DP.FM.029] Cross-Platform Path Leak

## Суть паттерна

В конфигурации или коде инструмента прописан **платформо-специфичный путь** (macOS slug `/Users/username`, Windows `C:\Users\...`, `-Users-...-IWE`). На целевой платформе (Linux/сервер) путь не существует, но инструмент:

1. Не завершается с ошибкой
2. Выдаёт только `WARN: Source not found` или аналог
3. Продолжает работу, молча пропуская затронутый раздел

На платформе-источнике (macOS у автора) баг не проявляется — пути там корректны.

## Диагностические признаки

- На production/CI тест проходит, но раздел «молча пропускается»
- WARN в логах содержит путь с `/Users/` или macOS-слагом
- На macOS-хосте — `exit 0`, на Linux — `WARN` + неполный результат
- Аудит §6 `iwe-audit.sh` сканирует `/etc/systemd`, `/etc/iwe`, `~/.config`, `.iwe-runtime`, `.claude` на наличие macOS-слагов

## Защита

1. **При промоции скрипта L3→L1:** проверять все строковые константы на platform-specific пути
2. **Smoke-test:** добавлять проверку «запускается из Linux-окружения без hardcoded macOS-путей»
3. **Параметризация:** выносить пути в `params.yaml` с `{{PLACEHOLDER}}`
4. **Env guard:** симметрично сбрасывать все env vars, потенциально затронутые тестом (см. DP.FM.017)

## Отличие от DP.FM.009

`DP.FM.009` — hardcoded путь *в протоколе/скрипте* без привязки к платформе. `DP.FM.029` — hardcoded путь *в конфиге кросс-платформенного инструмента* + эффект молчаливого пропуска (не аварии) на несовместимой платформе.
