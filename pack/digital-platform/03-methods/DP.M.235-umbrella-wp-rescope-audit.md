---
id: DP.M.235
name: "Audit зонтичного РП: rescope через promote/cancel/defer/spawn"
type: method
domain: digital-platform
pack_refs: ["DP.M.010"]
trust: medium
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "WP-150 rescope (commit 8d62bf0c), peer-session 2026-05-29-18-wp150-umbrella-audit-scope"
---

# DP.M.235 Audit зонтичного РП: rescope через promote/cancel/defer/spawn

## Описание

Зонтичный РП с длинной историей (≥10 фаз, ≥3 месяца) накапливает фазы в разных состояниях: завершённые, превзойдённые более поздним решением, требующие отдельного scope, off-scope. Без периодического audit зонтик расползается — невозможно сказать «что осталось».

Метод audit определяет 4 операции rescope для каждой открытой фазы.

## IPO

**Вход:** Зонтичный РП с N открытыми фазами; критерии done зонтика.

**Процесс (4 audit-операции):**

| Операция | Когда применять | Действие |
|----------|-----------------|----------|
| **Promote → child WP** | Фаза достаточно большая и автономная (≥3h работы, отдельный артефакт). | Вынести в отдельный РП с явным `parent_wp`. |
| **Cancel** | Фаза превзойдена другим решением ИЛИ признана overengineering. | Закрыть, зафиксировать обоснование в audit-log. |
| **Defer** | Гипотеза жива, но не приоритетно сейчас. | Перевести в offline-backlog с явным triggering condition. |
| **Spawn без parent** | При audit обнаружена работа, не относящаяся к зонтику. | Новый РП без `parent_wp`. |

**Выход:** Зонтик содержит только фазы, ведущие к единому артефакту; child/offline/spawn РП зарегистрированы.

## Триггеры audit

- Зонтик >3 месяца с момента открытия.
- >8 открытых фаз.
- Scope creep (несколько концепций смешаны в одном РП).
- Перед closing: проверка «остались ли только фазы, ведущие к артефакту?».

## Пример: WP-150 rescope (2026-05-29)

| Фаза | Операция | Итог |
|------|----------|------|
| Ф0, Ф6, Ф7, Block D | (done) | Артефакты сданы |
| Ф1 | Promote → child | WP-365 |
| Ф9 | Promote → child | WP-367 |
| Block E | Spawn без parent | WP-366 (multi-model ≠ multi-agent — другая работа) |
| Ф2 | Cancel | superseded Ф7 |
| Ф3 | Cancel | git достаточно |
| Ф4 | Cancel | overengineering |
| Ф5, Ф8 | Defer | offline — гипотеза жива, не было времени |

## Done-критерий зонтика после audit

«Остались ли только фазы, ведущие к единому артефакту?» Да → audit закрыт. Нет → повторить.

## Применение

Любые multi-phase governance артефакты: epic в Jira, milestone, OKR objective; зонтичные РП IWE; страт-сессионные блоки работы.

## Связи

- Источник: WP-150 rescope (commit 8d62bf0c), peer-session 2026-05-29-18
- DP.M.010 (WP lifecycle) — single-WP операции (open/work/close)
- WP-150 (umbrella audit triggering case)
