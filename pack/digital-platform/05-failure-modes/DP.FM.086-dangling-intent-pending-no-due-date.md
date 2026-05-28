---
id: DP.FM.086
name: "Dangling Intent: РП pending без dueDate"
type: failure-mode
pack: digital-platform
status: active
valid_from: 2026-05-28
source: peer-session 2026-05-28-01 (WP-353 hold-out анализ)
---

# DP.FM.086: Dangling Intent — pending без dueDate

## Симптом

РП или намерение находится в статусе `pending` без явного `due_date` и без явного блокера. Создаётся иллюзия «на паузе», но на практике — невидимый inventory без триггера возврата.

## Механизм

Разработчик/пилот откладывает принятие решения: «вернёмся когда-нибудь». Отсутствие `due_date` означает, что ни один процесс (watchdog, Day Open, Week Close) не сигнализирует о необходимости вернуться.

## Тест

«Есть ли что-то или кто-то, что явно сигнализирует о возврате к этому РП?» Нет → dangling intent.

## Различение

**Пауза** = явный блокер (`blocked_by: <причина>`) + ожидаемое время разблокирования.
**Dangling intent** = нет ни блокера, ни срока.

## Лечение

1. `due_date` + создать event в Google Calendar «IWE Platform Ops»
2. `status: blocked` + `blocked_by: <причина>` + `unblock_at: YYYY-MM-DD`

## Антипаттерн

«Поставим на паузу и вернёмся когда-нибудь» = Inventory без throughput (Goldratt). Данный FM — частный случай накопления незавершённого производства.

## Контекст

Обнаружен при анализе WP-353 в peer-session 2026-05-28-01. WP-353 имел статус `pending` без dueDate, что блокировало прогресс — Кими назвал это «dangling intent».
