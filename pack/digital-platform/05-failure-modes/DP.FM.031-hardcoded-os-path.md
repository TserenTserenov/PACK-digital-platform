---
id: DP.FM.031
title: "Hardcoded OS-путь вместо $HOME в скриптах IWE"
type: failure-mode
pack: digital-platform
severity: medium
status: active
trust: 0.9
epistemic_stage: validated
valid_from: 2026-05-13
related: [DP.FM.009, DP.FM.029, DP.IWE.002]
---

# DP.FM.031 — Hardcoded OS-путь вместо $HOME в скриптах IWE

## Определение

Скрипт IWE (мониторинг, health-check, scaffold) использует абсолютный OS-специфичный путь (`/home/tseren/IWE/...` или `/Users/tserentserenov/IWE/...`) вместо `$HOME` или `$IWE_WORKSPACE`. При запуске на другой ОС или с другим именем пользователя — путь не существует, но скрипт выдаёт только предупреждение без явной ошибки (silent fail).

**Отличие от DP.FM.009:** FM.009 — протокол ссылается на путь к самому скрипту. DP.FM.031 — скрипт ссылается на данные/артефакты через OS-специфичный путь.
**Отличие от DP.FM.029:** DP.FM.029 — leak в конфиге кросс-платформенного инструмента. DP.FM.031 — скрипт использует abs path к собственным данным.

## Наблюдаемые симптомы

- Скрипт показывает 🟡/🔴 на одной машине и 🟢 на другой при одинаковых данных
- Лог: `WARN: Source not found` или `File does not exist` без прерывания исполнения
- `gate_log.jsonl` не найден при локальном запуске на macOS (путь `/home/...` вместо `/Users/...`)
- `iwe-audit.sh §6` обнаруживает macOS-слаги в конфигах cross-platform скриптов

## Примеры

### gate_log false positive (13 мая 2026)

`day-open-scaffold.sh` ищет `gate_log.jsonl` по пути `/home/tseren/IWE/...`. На macOS реальный путь `/Users/tserentserenov/IWE/...` → файл существует (208 900 байт), но скрипт не находит → false positive 🟡 в day-open отчёте.

## Стратегии предотвращения

1. **$HOME вместо абсолютного пути.** Все скрипты строят пути через `$HOME` или `$IWE_WORKSPACE`.
2. **Smoke-test на Linux.** При промоции скрипта L3→L1 — проверить запуск в Linux-окружении.
3. **iwe-audit.sh §6.** Регулярный аудит macOS-слагов в cross-platform конфигах.
4. **Полный env var guard.** Smoke-тесты сбрасывают ВСЕ env vars с эфемерными путями симметрично.

## Диагностика

```bash
# Поиск OS-специфичных путей в скриптах IWE
grep -r "/Users/" ~/IWE/FMT-exocortex-template/scripts/
grep -r "/home/" ~/IWE/FMT-exocortex-template/scripts/
# Аудит через iwe-audit.sh §6
bash $IWE_TEMPLATE/scripts/iwe-audit.sh 6
```

## Связанные артефакты

- DP.FM.009: Схожий паттерн, hardcoded path к скрипту
- DP.FM.029: Cross-platform path leak (другой scope — конфиг)
- DP.IWE.002 (IWE template) — контекст
