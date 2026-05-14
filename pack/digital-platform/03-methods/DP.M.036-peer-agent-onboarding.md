---
id: DP.M.036
title: "Онбординг peer-агента в Local Gateway IWE"
type: method
pack: digital-platform
status: draft
trust: 0.8
epistemic_stage: observed
valid_from: 2026-05-13
related: [DP.IWE.005, DP.ROLE.039, DP.SC.035]
---

# DP.M.036 — Онбординг peer-агента в Local Gateway IWE

## Описание

Метод подключения нового peer-агента к Local Gateway IWE (DP.IWE.005). Каждый агент получает отдельную запись в `.mcp.json` с уникальным `IWE_AGENT_ID`, идентифицируется через переменную среды, а не сетевой адрес.

## Шаги

1. Установить расширение агента в IDE (пример: moonshot-ai.kimi-code).
2. Добавить запись в `.mcp.json` (IWE root):
   ```json
   "iwe-local-gateway-{agent-id}": {
     "command": "...",
     "env": { "IWE_AGENT_ID": "{agent-id}" }
   }
   ```
3. Создать `AGENTS.md` в корне IWE — инструкции для нового агента (роль, ограничения, контекст).
4. Верифицировать подключение: проверить лог gateway на `agent connected: {agent-id}`.
5. Smoke-test параллельной работы: дать каждому агенту задачу на разных файлах.

## Инварианты

- Каждый peer-агент имеет **уникальный `IWE_AGENT_ID`** — идентификация в gateway через env var, не network address.
- Агенты не делят одну gateway-запись (нет shared entry).
- При конфликте — lock-протокол (DP.SC.035).

## Связи

- Сущность: DP.IWE.005 Local Gateway
- Роль: DP.ROLE.039 Peer Agent
- SC: DP.SC.035 Peer Coordination
