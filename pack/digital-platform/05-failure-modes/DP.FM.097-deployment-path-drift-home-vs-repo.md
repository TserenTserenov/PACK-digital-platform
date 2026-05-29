---
id: DP.FM.097
name: Deployment Path Drift — Home vs Repo
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-28
source: session-transcript 2026-05-28 (WP-358 Fix 4 Root Cause A)
---

# DP.FM.097 — Deployment Path Drift: Process Runs from /home/user/, Not from Repo

## Описание

Разработчик вносит правки в git-репо, делает push — но на сервере работает копия файла в `/home/user/` (из прошлого ручного деплоя). Процесс или systemd-unit настроен на путь вне репо → git pull не влияет на работающий процесс.

## Симптом

Фикс в репо не даёт эффекта. «Всё правильно в коде, баг воспроизводится» — потому что реальный исполняемый файл не совпадает с файлом в репо.

## Диагностика

```bash
ps aux | grep <process>              # проверить путь в PID
systemctl cat <service>              # найти ExecStart → реальный путь
diff <path_on_server> <path_in_repo> # убедиться в расхождении
```

## Паттерн

При деплое нового скрипта: ExecStart в systemd-unit и git-путь должны совпадать или связываться через symlink. Ручные копии вне репо = deployment drift.

**Тест:** `diff $(systemctl cat <service> | grep ExecStart= | cut -d= -f2) $(git -C ~/IWE show HEAD:<path-to-script>)` → любой diff = FM активен.

## Связанные FM

- DP.FM.028 — git-pull-in-production (production системы без immutable artifact)

## Применимо

Любой сервис, запускаемый через systemd, cron, launchd, где ExecStart/Command указывает на путь вне git-репо.
