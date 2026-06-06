---
id: DP.M.272
title: "Role unpacking через split_to: распаковка одного паспорта в два + обновление DP.MAP"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-04
source: peer-session 2026-06-04-30 (WP-378 Ф0 — DP.ROLE.012 split → DP.ROLE.066), git diff PACK-digital-platform ba45bed (DP.ROLE.012-strategist + DP.ROLE.066-planner), DS-my-strategy 203327111 (ID 064→066)
related:
  see_also: [DP.METHOD.010, DP.M.236]
---

# DP.M.272 — Role unpacking via split_to

## Описание

Когда роль несёт две разные функции (одна и та же роль исполняет и X, и Y), распаковка через паттерн split — четыре атомарных шага, заканчивающихся обновлением карты надсистемы.

## Алгоритм

1. **Сужение исходной роли.** В `00-role-passport.md` сузить миссию до одной функции (X), убрать упоминания второй (Y).
2. **Поле `split_to` в frontmatter.** Добавить `split_to: [DP.ROLE.NNN]` в frontmatter исходной роли — указатель на новую роль.
3. **Создание новой роли.** Собрать паспорт новой роли (`DP.ROLE.NNN/00-role-passport.md`) для функции Y.
4. **Обновление карты ролей (DP.MAP.001).** Обе роли — отдельные узлы в карте, со связями к старой (split_from) и новой (split_to).

## IPO

- **Input:** перегруженная роль `DP.ROLE.X` с двумя функциями + DP.MAP.001.
- **Process:** сужение паспорта X → объявление split → создание новой роли Y → обновление карты.
- **Output:** два паспорта (X сужен, Y новый) + обновлённая DP.MAP.001 с двумя узлами.

## Подводный камень: ID-резервирование vs финальный ID

**Антипаттерн:** «зарезервировал ID 064, написал body, потом ID съехал на 066» → body-header не совпадает с финальным ID → `pack-lint` ловит, ручной фикс.

**Правило:** ID финализируется ДО написания body, или skip-step при разрыве (генерация body после получения окончательного ID).

## Обобщение

Разделение функционального места всегда требует обновления карты надсистемы, не только паспортов ролей. Применимо к:

- Split роли (этот метод).
- Split сервиса (DP.SC) — карта сервисов.
- Split обещания.
- Split метода (если метод оказался гибридом).

## Связи

- DP.METHOD.010: kinds-owner-roles (kind/owner role identification — предусловие split)
- DP.M.236: phase-split-by-verification-class (split фаз РП по верификации — другая ось split'а)
- AR.226: role-id-collision-prevention-grep-before-assign (защита от ID-коллизии при создании новой роли)
