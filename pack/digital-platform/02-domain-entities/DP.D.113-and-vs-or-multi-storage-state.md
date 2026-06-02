---
id: DP.D.113
name: "AND-семантика ≠ OR-семантика для multi-storage state"
type: distinction
status: active
valid_from: 2026-05-30
summary: "Когда состояние сущности разнесено между volatile + durable storage'ами: AND-семантика (активна если оба источника подтверждают) требует orphan recovery loop; OR-семантика (активна если хотя бы один) безопаснее для doubt cases."
related:
  see_also: []
wp: WP-358 Ф10
---

# AND-семантика ≠ OR-семантика для multi-storage state (DP.D.113)

> Различение архитектурных режимов для сущности, состояние которой разнесено между двумя storage'ами — volatile (FSM в RAM/БД) и durable (файлы/append-only журнал).

## AND-семантика

**Что:** сущность активна, если **оба** источника подтверждают (`fsm.state == active` И `durable.file exists`).

**Когда применять:** volatile-сторона несёт authoritative state (FSM решает что считать ходом), durable — append-only журнал свидетельств.

**Failure case:** volatile сбрасывается (Railway redeploy, idle timeout, миграция схемы) → durable файлы остаются «висящими» без active владельца → `/close` возвращает «нет активной сессии», файл остаётся неоформленным.

**Требует:** orphan recovery loop.

- Periodic scan durable-side с фильтром «нет active-FSM».
- Finalization logic (close-with-best-effort vs reopen-from-durable).
- Дедупликация, чтобы recover не сработал дважды.

## OR-семантика

**Что:** сущность активна, если **хотя бы один** источник подтверждает.

**Когда применять:** doubt cases, где «лучше показать активной, чем потерять».

**Failure case:** «zombie active» — durable говорит активно, FSM забыл → пользователь видит активную сессию без backend-логики.

**Требует:** explicit reaper (background-задача со staleness-порогом).

## Тест применимости

«Есть ли в системе две storage'а одной сущности, один volatile, один durable, с AND-семантикой?» Да → spec orphan recovery до прод-выкатки, иначе incidents post-deploy.

## Применимо к

- Сессионные архитектуры с FSM (Aiogram + БД)
- Distributed locks (Redis + БД)
- Workflow engines с execution state
- Любое state machine с separate audit log