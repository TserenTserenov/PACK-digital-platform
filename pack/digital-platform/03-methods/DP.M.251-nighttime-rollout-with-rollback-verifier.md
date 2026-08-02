---
id: DP.M.251
name: Nighttime Rollout with Pre-Deploy Rollback and Post-Deploy Verifier
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
last_updated: 2026-08-01
source: DS-my-strategy WP-330 С9b context + rollback.sql (commit 5e4782a53) + peer-session 2026-05-31-13
---

# DP.M.251 — Ночной роллаут: rollback до деплоя + verifier после

## Суть

DB-migration + code deploy при наличии активных пользователей:
три обязательных артефакта + ночное окно = минимизация риска инцидента.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость деплоя vs. готовность rollback | Написание и тестирование rollback.sql до деплоя замедляет релиз; метод принимает замедление ради гарантированного пути отката |
| Thoroughness verifier vs. ночное окно | Post-deploy verifier нуждается во времени, чтобы поймать проблемы, но окно ночи ограничено; метод балансирует cold-context smoke с временем на rollback до утреннего трафика |
| Минимизация затронутых пользователей vs. observability | Ночью меньше пользователей, что снижает impact инцидента, но и меньше глаз для обнаружения проблем; метод парует низкий трафик с deliberate verifier |

## Три обязательных артефакта

1. **rollback.sql** — заготавливается ДО деплоя (не после обнаружения проблемы)
2. **post-deploy verifier** — агент с холодным контекстом, smoke после деплоя
3. **wording-fix** — согласование формулировок, найденных verifier'ом

## Порядок шагов

```
1. [Pre-deploy]  Написать rollback.sql → закоммитить рядом с migration
2. [Pre-deploy]  Проверить rollback.sql на синтаксис
3. [Deploy]      Запустить деплой в ночное окно (минимум активных пользователей)
4. [Post-deploy] Запустить post-deploy verifier (cold context)
5. [Post-deploy] Применить wording-fix по результатам verifier
6. [Confirm]     PASS → закрыть фазу
```

## Ночное окно

Цель: минимизировать число затронутых пользователей при инциденте.
Есть время на manual rollback до утреннего трафика.

## Ключевое правило

`rollback.sql` пишется когда есть время → при инциденте нет времени.
Написание "после" = отсутствие rollback в критический момент.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Rollback-as-afterthought | Практикующий пишет rollback.sql только при возникновении проблемы, потому что предварительная подготовка воспринимается как планирование неудачи |
| Verifier optimism | Практикующий пропускает post-deploy verifier, считая, что ночное окно достаточно спокойно, и убирает safety net, который метод парует с низким трафиком |
| Over-reliance on night window | Практикующий трактует тихое окно как достаточную защиту и деплоит миграции, всё ещё требующие ручной проверки, сжимая время rollback |

## Применимость

- DB-migration на prod с активными пользователями (любой стек)
- Деплой с изменением структуры данных (dual-key, renames, splits)
- Любой деплой с необратимыми side effects

## Граница

Не применять для stateless deploys без DB-migration — избыточно.

## Связи

- pack_refs: WP-330 С9b (первый задокументированный случай применения)
- rollback.sql: DS-my-strategy/inbox/WP-330/ (reference artifact)

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
