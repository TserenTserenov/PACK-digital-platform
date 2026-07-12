---
id: DP.M.257
type: method
domain: digital-platform
status: active
valid_from: 2026-05-30
source: peer-session 2026-05-30-13-wp353-closure-decision-n3; commits 476f4b13, 6daa00e5 (WP-353 Honcho)
---

# DP.M.257: `closed-partial` (partial-success) + multi-channel resumption

## Контекст

РП имеет естественный «threshold возобновления» (n≥5 hold-out, n+30 дней, новая версия зависимости). Пилот не готов держать его «в активном списке» 1-3 месяца, но и закрыть как `done` нельзя — получен результат только для части scope.

## Closure class

`closure_class: partial-success` — фазы Ф1/Ф2/Ф3 завершены, v2 нужен при достижении threshold Y.

## Multi-channel resumption stack

Одного канала недостаточно — reminder можно потерять, process-catalog можно отключить, Backlog можно забыть. **Пять каналов** с разной семантикой, каждый со своим owner-ом и режимом срабатывания:

1. **Proactive script** в `process-catalog A-класс` (weekly check скрипт) — проверяет условие threshold, эмитит сигнал.
2. **Proactive launchd plist** — независимый таймер (1× в неделю), не зависит от process-catalog.
3. **Reactive reminder** (TTL one-shot) — в дату ожидаемого threshold (например, +30 дней).
4. **Human-readable Backlog B-NNN** с альтернативой и обоснованием — пилот видит при ревизии бэклога.
5. **Inline `EXPERIMENTAL` comment** в коде/DDL с TTL-маркером — увидит любой, кто откроет файл.

Robust resumption даже при потере любых 2-3 каналов из 5.

## Тест выбора паттерна

«Есть ли уже частичный результат?»
- Нет → `deferred-with-explicit-triggers` (lessons_defer_with_explicit_triggers.md, WP-150).
- Да → `closed-partial` (этот метод).

## Failure mode альтернативы

Один Backlog-пункт без multi-channel: через 3 месяца «о чём это» — теряется контекст спеки и обоснования. Один процесс-catalog без human-readable Backlog: пилот не видит при стратегировании.

## Прецедент

WP-353 (Honcho) — закрыт `closed-partial` 30 мая 2026 после Ф1-Ф3 (commits 476f4b13, 6daa00e5). v2 ожидается при threshold (новая версия Honcho API или n≥5 повторных incidents).

## Связи

- **Дополняет:** `memory/lessons_defer_with_explicit_triggers.md` (WP-150, 29 мая) — соседний паттерн для другого случая.
- **Соседний:** DP.M.218-1 (если потребуется) для full `deferred-with-explicit-triggers` formalization в Pack.