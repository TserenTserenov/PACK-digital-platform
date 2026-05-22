---
id: DP.M.138
type: method
name: "Dispatcher: синхронизация origin и идемпотентная запись результата после headless-агента"
domain: digital-platform
status: draft
trust: medium
epistemic_stage: instance
source: "FMT-exocortex-template commit ea7ead8, fix dispatcher race-condition after claude -p"
valid_from: 2026-05-21
related:
  complements: [DP.M.112]
---

# Dispatcher: синхронизация origin и идемпотентная запись результата после headless-агента

## Суть

Headless-агент (claude -p) может коммитить свои результаты в репо во время выполнения. После возврата `claude -p` локальный HEAD dispatcher'а может быть позади origin. Если dispatcher немедленно пишет статусный коммит — возникает конфликт или перезапись изменений агента.

## Алгоритм

1. `git fetch origin` + `git reset --hard origin/<branch>` — синхронизировать HEAD с origin
2. Проверить: существует ли result-файл, ожидаемый от агента?
   - **Да** — агент уже записал результат, пропустить `write_result`
   - **Нет** — записать result-файл от имени dispatcher'а
3. Только после (2) делать коммит с итоговым статусом

## Инвариант

Запись результата идемпотентна: если агент уже записал — dispatcher не перезаписывает. Гарантирует атрибуцию (кто реально написал) и целостность данных.

## Применимость

Любая dispatcher/agent архитектура с разделённым репо через git. Не зависит от конкретного headless-инструмента.

## Антипаттерн

Dispatcher коммитит сразу после return без `fetch` + `reset --hard` → push конфликт или тихая перезапись агентских коммитов.
