---
id: DP.FM.194
title: "launchd не перезапускает сервис, пока stale-pid занял порт (EADDRINUSE)"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / infrastructure / macOS-launchd
epistemic_stage: confirmed
valid_from: 2026-07-03
source: "session-close 2026-07-03 (sessions/2026-07-03-watchdog-llm-proxy-stale.md)"
related:
  see_also: [DP.FM.143]
---

# DP.FM.194 — launchd не перезапускает сервис, пока stale-pid занял порт

## Описание

launchd с `KeepAlive = true` пытается перезапустить упавший/зависший сервис. Если stale-процесс жив и держит TCP-порт, каждая попытка launchd завершается `EADDRINUSE` → цикл ошибок в `launchd-<service>-err.log` без прогресса. Сервис никогда не поднимается автоматически.

## Симптом

- `launchd-<service>-err.log` содержит серию `Address already in use` ошибок
- Сервис не отвечает, но stale-процесс виден через `ps aux`
- launchd уверен что сервис «падает», но причина — не сбой логики, а занятый порт

## Диагностика

```bash
lsof -i :<PORT>            # найти stale pid, удерживающий порт
ps aux | grep <stale_pid>  # проверить возраст процесса
```

**Caveat:** порт может казаться свободным при `lsof`-проверке (race condition между попыткой launchd и проверкой) — проверять по pid процесса, не только по `lsof`.

## Фикс

```bash
kill <stale_pid>   # launchd автоматически поднимает новый инстанс
```

## Профилактика

При появлении серии `EADDRINUSE` в launchd-err.log — **не искать ошибку конфигурации plist**. Сразу: `lsof -i :<PORT>` → найти stale pid → kill.

## Тест обнаружения

```bash
grep "Address already in use" ~/Library/Logs/launchd-<service>-err.log | wc -l
# > 3 строк = FM активен
```

## Связи

- DP.FM.143 (PPID-fallback при stale PID в pidfile) — смежный: там PID переиспользован ОС; здесь процесс жив и держит ресурс
- Применимо к любому macOS launchd plist с `KeepAlive = true` и сетевым портом
