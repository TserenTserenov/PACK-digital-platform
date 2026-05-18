---
id: DP.D.068
name: Discovered-WP vs Discoverer-WP — owner-routing бага из post-hoc audit'а
kind: Distinction
status: active
created: 2026-05-17
sources:
  - WP-310 bug-file 027f3d6d «Обнаружено в post-hoc audit WP-326. Owner: WP-310 Аттестатор, не WP-326»
  - captures.md 2026-05-17 session-close-feed (WP-326 close)
related:
  complements:
    - feedback_fixes_as_phases_not_separate_wp.md  # in-scope фиксы — фазы того же РП
  contrasts_with:
    - DP.D.064-same-vs-different-promise-wp-branch  # внутри одного промиса vs между промисами
---

# DP.D.070: Discovered-WP ≠ Discoverer-WP при owner-routing бага из audit'а

## Различение

| Аспект | Discovered-WP (тот, чью функциональность сломали) | Discoverer-WP (тот, чей audit нашёл баг) |
|--------|-------------------------------------------------|-----------------------------------------|
| Кто **владеет** багом | Owner функциональности, в которой обнаружен дефект | НЕ владеет; только сообщает |
| Кто **чинит** | Owner-роль discovered-WP (новая фаза, hotfix-WP или backlog) | НЕ чинит (иначе — scope-creep) |
| Где **регистрируется** bug-файл | `inbox/bugs/` с `owner: <discovered-WP>` | НЕ в скоупе своего РП |
| Что делает discoverer | Закрывает свой РП без scope-creep'а; помечает discovery в Close-отчёте | — |

## Тест применимости

> «Audit/verifier РП-A нашёл баг функциональности X, owned ролью Y (Y ≠ роль РП-A)?»

- **Да** → bug-файл с `owner: WP-of-Y`, fix как фаза WP-of-Y (или hotfix в той же сессии с owner=Y).
- **Нет** (баг внутри scope discoverer'а) → fix в текущей фазе того же РП (см. `feedback_fixes_as_phases_not_separate_wp.md`).

## Антипаттерн

Втягивание fix'а discovered-функциональности в discoverer-WP:
- Нарушает scope границы (РП-A claim'и done за функциональность роли Y).
- Разрастает РП (бюджет течёт).
- Теряет owner-accountability (Y не учится на собственных ошибках; audit становится «закрой за меня»).

## Прецедент

WP-326 (Диагност 5-step pseudocode + verifier-rounds) post-hoc audit нашёл `cp_assessments` permission denied — компетенция WP-310 Аттестатора, не WP-326 IWE Stage Controller'а. Bug-файл `inbox/bugs/027f3d6d.md` создан с `owner: WP-310`. Fix добавлен как Ф14 WP-310 (hotfix в той же сессии). WP-326 закрылся без scope-creep'а.

## Связи

- Дополняет `feedback_fixes_as_phases_not_separate_wp.md` (in-scope фиксы — фазы того же РП).
- Противопоставляется `DP.D.064` (same-vs-different-promise) — D.064 решает «один РП или два», D.070 решает «куда зарегистрировать bug после audit'а».
- Применяется вместе с `AR.104` (independent review): review = механизм нахождения, D.070 = механизм маршрутизации найденного.
