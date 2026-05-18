---
id: DP.M.082
name: "WP scope boundary через DP.SC interfaces"
description: "Метод управления crowded scope: соседние РП связываются через явные DP.SC interface-ссылки (uses/realizes/extends), а не через merge scope."
type: method
status: active
valid_from: 2026-05-17
domains: [wp-management, scope, service-clauses, interface-decoupling]
trust: 3
epistemic_stage: stable
source: WP-324 commit ba1902c4 (раздел «Что НЕ входит в этот РП (граница scope)» с таблицей 5 родственных РП)
relates: DP.D.064 (same-vs-different-promise), DP.D.069 (artifact-vs-mode), DP.M.048 (scope-discipline-wp-closure), DP.SC template
---

# DP.M.063: WP scope boundary через DP.SC interfaces

## Когда применять

У РП появляются «соседние» родственные работы (Q&A history, Trace store, Hermes memory, real-time channel, системные правила, общие компоненты). Соблазн merge: «это близко по теме, объединим в один РП». Результат: scope инфлируется до неподъёмного, обещание размывается, owner перегружен.

## Метод

1. **Каждый РП реализует своё обещание** (`realizes: DP.SC.NNN`).
2. **Связки с соседями — через явные SC-ссылки**:
   - `uses: DP.SC.MMM` — потребляет обещание соседа.
   - `realizes: DP.SC.NNN` — реализует это обещание (выход своего РП).
   - `extends: DP.SC.KKK` — расширяет обещание соседа (specialization).
3. **При закрытии РП** достаточно проверить, что собственное SC-обещание выполнено. Соседи доберут своё в своих РП.

## Артефакт паттерна

Раздел «**Что НЕ входит в этот РП (граница scope)**» в WP-context-файле с таблицей:

| Возможная работа | Куда относится | Почему не сюда |
|------------------|----------------|----------------|
| Q&A history | WP-XXX (отдельный РП) | Другое обещание (хранение vs маршрутизация) |
| Trace store | WP-295 (planned) | Inbox = транзит, Trace = архив (разные жизненные циклы) |
| Hermes memory | WP-316 | Память агента ≠ inbox задач |
| Real-time channel | WP-203 Ф6 | Режим артефакта, не новый артефакт (см. DP.D.069) |

## Тест

**«Если читатель спросит "почему не объединили X с этим РП" — есть ли явный ответ в WP-context?»**

- **Только в голове owner'а** → недостаточно. Нужна таблица «Что НЕ входит».
- **В таблице** → scope boundary защищена документально.

## Анти-паттерн

«Соседние темы — все вместе» → 1 zonтик-РП на 50ч с размытым обещанием. Owner не знает, когда РП закрыт; ArchGate не проходит (несколько архитектурных решений в одном).

## Пример (WP-324 Agent Inbox)

5 соседних работ:
- Trace store (WP-295) — `extends: DP.SC.NNN-agent-trace`
- Hermes memory (WP-316) — separate WP, no interface (разные scope)
- Notification Dispatcher (WP-320) — `uses: DP.SC.MMM-notification-dispatch` для P0-уведомлений
- Real-time channel — `extends: DP.SC.NNN` (WP-203 Ф6, не отдельный РП — см. DP.D.069)
- Системные правила (CCR ↔ inbox protocol) — `uses: DP.SC.KKK-ccr-runtime`

Каждая работа сохраняет связь через interface, не через merge scope.

## Применимость

- Любой crowded scope (Agent Inbox, Hermes, ЦД→Память migration).
- Zonтик-РП с подфазами — каждая подфаза имеет своё SC, родитель агрегирует.
- Обсуждения «расширим scope или новый РП».

## Различение

- **DP.D.064** (same-vs-different-promise-wp-branch) — общий дискриминатор «фаза vs РП».
- **DP.D.069** (artifact-vs-artifact-mode) — частный случай для single-artifact + N modes.
- **DP.M.063** (этот) — рецепт для multi-artifact crowded scope через interface-decoupling.
- **DP.M.048** (scope-discipline-wp-closure) — дисциплина scope при закрытии РП; DP.M.063 — дисциплина scope при проектировании РП.
