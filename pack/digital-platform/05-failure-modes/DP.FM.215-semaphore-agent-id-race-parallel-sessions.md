---
id: DP.FM.215
type: failure-mode
title: "Семафор-указатель ключуется по agent-id, а не session-id — гонка при параллельных сессиях одного агента"
pack: PACK-digital-platform
domain: platform-agents
status: draft
valid_from: 2026-07-07
source: "session-close 2026-07-04 (WP-464, DS-my-strategy d66453b, bug-2026-07-04-session-guard-note-file-doesnt-unblock-existing-files.md)"
schema_version: 1
related:
  see_also: [DP.FM.194]
---

# DP.FM.215: Семафор ключуется по agent-id → гонка при параллельных сессиях

## Описание

Файл-семафор (указатель активной сессии) именуется по имени агента (`current-claude-code.ptr`), а не по уникальному идентификатору сессии. При двух параллельных сессиях с одинаковым `agent-id` обе пишут в один файл-указатель — close одной сессии может по гонке разблокировать ресурсы другой.

## Класс дефекта

Race condition: неявное разделение состояния между независимыми параллельными процессами через общий ключ.

## Ситуация возникновения

```
session-guard.sh open  # Сессия A пишет в current-claude-code.ptr
session-guard.sh open  # Сессия B пишет в тот же current-claude-code.ptr (перезаписывает)
session-guard.sh close # Сессия A закрывается → удаляет ptr → Сессия B теряет семафор
```

Реальные последствия: разблокировка ресурсов живой сессии (close одной сессии убирает guard другой), некорректная атрибуция note-file, невозможность различить «кто владеет семафором».

## Диагностика

- `ls ~/.iwe-sessions/current-*.ptr` → одна запись на agent-id вместо одной на session
- При двух параллельных сессиях одного агента `wc -l` показывает только один ptr-файл

## Фикс

Ключевать файл-указатель по `<agent-id>-<session_id>` или по PID-стартовой-сессии:

```bash
# Было (уязвимо):
PTR_FILE="$SEMAPHORE_DIR/current-${AGENT_ID}.ptr"

# Стало (per-session):
PTR_FILE="$SEMAPHORE_DIR/current-${AGENT_ID}-${SESSION_ID:-$$}.ptr"
```

**Workaround до фикса:** не вызывать ручной `close` без уверенности в `session_id`; дать семафорам истечь по TTL (30 мин).

## Связи

- DP.FM.194 (launchd stale PID) — смежный класс «liveness по неверному идентификатору»
- WP-464 (session-guard.sh): место исправления
