---
id: DP.FM.022
type: failure-mode
name: systemd-minimal-path
title: "Systemd minimal PATH: команды без абсолютного пути дают пустой вывод"
domain: digital-platform
pack: PACK-digital-platform
valid_from: 2026-05-12
status: active
schema_version: 1
---

# DP.FM.022 — Systemd minimal PATH

## Симптом

systemd-юнит запускается с ограниченным `PATH` (нет `/usr/bin`, `/usr/local/bin`). Команды без абсолютного пути (`hostname`, `date`, `python3`) возвращают пустой вывод или `command not found` без явной ошибки в логе.

**Пример:** `$(hostname)` → пустая строка → `meta.yaml` содержит `host: ""` вместо реального имени хоста.

## Причина

systemd по умолчанию устанавливает минимальный `PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`. На некоторых дистрибутивах (Debian/Ubuntu) `/usr/bin/hostname` доступен, но `/bin/hostname` — нет, или наоборот.

## Паттерн защиты

Каскад fallback-источников для одного значения:

```bash
HOST=$(hostname 2>/dev/null || cat /etc/hostname 2>/dev/null || echo "unknown")
```

Альтернатива: явный `PATH=/usr/bin:/bin:$PATH` в начале скрипта или `Environment=PATH=...` в `.service`-файле.

## Применимость

Все shell-скрипты, запускаемые через systemd/launchd. Особенно критично для overnight-агентов и CI-подобных задач, где пустое значение молча кладётся в файл без ошибки.

## Связи

- Уточняет: DP.FM.010 (agent failure patterns) — частный случай env-contamination
- Реализация-пример: `DS-autonomous-agents/…/overnight-auditor.sh` (commit 6181f6b)
