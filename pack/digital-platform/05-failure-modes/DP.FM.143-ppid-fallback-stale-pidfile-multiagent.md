---
id: DP.FM.143
type: failure-mode
title: "PPID-fallback при stale PID в pidfile агентного скрипта"
description: "pidfile хранит PID шелл-процесса; при завершении шелла PID может быть переиспользован другим процессом → stale PID. Фикс: PPID как fallback."
pack: PACK-digital-platform
domain: digital-platform
valid_from: 2026-06-06
schema_version: 1
---

# DP.FM.143 — PPID-fallback при stale PID в pidfile агентного скрипта

## Описание

Pidfile-паттерн для отслеживания агентных процессов хранит PID текущего шелл-процесса. При завершении шелла ОС может переиспользовать PID для другого процесса — pidfile становится stale, но указывает на «живой» PID чужого процесса.

Фикс: при чтении pidfile — если основной PID недоступен или принадлежит чужому процессу — использовать PPID (PID родительского процесса) как fallback. PPID стабилен в рамках сессии VS Code / tmux / launchd.

## Симптом

Агентный скрипт (kimi-peer-adapter.sh, agent-status-report.sh) регистрирует себя живым при уже завершившейся сессии. Или: перезаписывает статус другого агента (idle → working) при параллельной работе.

## Дополнительный паттерн: status guards

Агент обязан проверить текущий статус (`idle` / `peer-session`) ПЕРЕД обновлением. Без проверки — параллельный агент перезаписывает чужой статус в shared status registry.

```bash
current_status=$(get_peer_status "$AGENT_ID")
if [[ "$current_status" == "idle" || "$current_status" == "peer-session" ]]; then
  update_peer_status "$AGENT_ID" "working"
fi
```

## Тест

«Если скрипт завершился и другой процесс получил тот же PID — pidfile всё ещё укажет на правильный агент?» Нет без PPID-fallback → FM.

## Применимость

Любая multi-agent среда с shared status registry и pidfile-паттерном.

## Связи

- Commit: `b24c91e` (kimi-peer-adapter.sh, FMT-exocortex-template)
- See: DP.FM.135 (projection-rule backfill), DP.D.084 (workspace-координация peer-агентов)
