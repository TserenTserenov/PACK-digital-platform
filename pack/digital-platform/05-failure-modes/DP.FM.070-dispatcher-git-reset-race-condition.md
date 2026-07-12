---
id: DP.FM.070
name: Dispatcher Git Reset Race Condition
name_ru: Состояние гонки в диспетчере — git reset --hard после claude -p
type: failure-mode
domain: headless-agent-coordination
trigger: claude -p завершается → диспетчер записывает RESULT.md → конфликт с агентскими коммитами
status: active
source: WP-337/WP-348 (FMT-exocortex-template ea7ead8, DS-my-strategy 54b3e84a)
created: 2026-05-21
---

# DP.FM.070 — Состояние гонки в диспетчере после claude -p

## Описание

Headless-диспетчер запускает `claude -p`, агент выполняет работу и делает git commits + push. После возврата `claude -p` локальный HEAD отстаёт от origin. Диспетчер пытается записать RESULT.md — возникает конфликт или перезапись агентских коммитов.

## Сценарий проявления

1. `claude -p "задача"` запущен диспетчером
2. Агент выполняет работу: коммиты + push в origin
3. `claude -p` возвращает управление диспетчеру
4. Диспетчер вызывает `git commit` + пишет RESULT.md → конфликт (HEAD behind origin) или потеря агентских коммитов при --force

## Фикс

Канонический алгоритм фикса (fetch+reset, идемпотентная проверка result-файла) — [DP.M.138](../03-methods/DP.M.138-dispatcher-origin-sync-after-headless-agent.md). Не дублируется здесь.

## Применимость

Любой headless-координатор агентов с общим git-репо как шиной (task-dispatcher pattern). Универсальный паттерн для систем где агент и диспетчер используют один репозиторий для обмена.

## Связи

- Реализован в: `DP.IWE.011-adapters/` (headless-adapter)
- Контекст: DP.IWE.011 (Runtime Host Contract)
- Метод фикса: [DP.M.138](../03-methods/DP.M.138-dispatcher-origin-sync-after-headless-agent.md) — тот же commit-источник (`ea7ead8`), этот файл описывает симптом, DP.M.138 — алгоритм
