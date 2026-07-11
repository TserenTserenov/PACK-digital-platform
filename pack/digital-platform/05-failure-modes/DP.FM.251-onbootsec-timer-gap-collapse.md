---
id: DP.FM.251
name: "OnBootSec gap collapse: начальный зазор между таймерами схлопывается при долгой uptime"
type: fm
pack: PACK-digital-platform
domain: digital-platform / infrastructure-scheduling
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-10, iwe-server-config commit 1055eb8 (tsekh-timer-race fix)"
see_also: [DP.M.023]
schema_version: 1
---

# DP.FM.251 — OnBootSec gap collapse: зазор между таймерами схлопывается при долгой uptime

**Суть:** `OnBootSec=N` задаёт зазор между таймерами только при старте системы. После N/T тиков (где T — `OnUnitActiveSec`) таймеры с одинаковым периодом синхронизируются и запускаются одновременно.

## Механизм

```
Timer A: OnBootSec=5min, OnUnitActiveSec=3h  → старт в t=5min
Timer B: OnBootSec=20min, OnUnitActiveSec=3h → старт в t=20min
Зазор при старте: 15 минут

После перезагрузки через 72 дня:
A тикает: 5, 185, 365, ... минут
B тикает: 20, 200, 380, ... минут
Через 5 тиков: 5 + 5×180 = 905, 20 + 4×180 = 740 — разные
Но дрейф OnUnitActiveSec накапливается независимо для каждого таймера.
При долгой uptime LCM(A,B) → схождение к одному моменту.
```

## Диагностика

Прямая: `systemctl list-timers` — если два таймера показывают одинаковый `NEXT` с разницей <1 сек — они схлопнулись.

## Принцип

«Начальный зазор ≠ постоянный зазор». `OnBootSec` задаёт только первое смещение. Для устойчивого разделения нужна:
1. Разная частота `OnUnitActiveSec` у двух таймеров, ИЛИ
2. Явная межпроцессная блокировка, не зависящая от расписания

## Фикс

Файловая блокировка (`flock`) на общем lock-файле — гарантирует взаимоисключение независимо от того, совпали ли таймеры (см. DP.M.372).

## Применимо

Любые два systemd-таймера с одинаковым `OnUnitActiveSec` и разным `OnBootSec`, работающие с общими ресурсами (git-репозиторий, БД, файловая система).
