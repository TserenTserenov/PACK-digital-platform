---
id: DP.M.234
name: "Двухусловное определение «открыто» для гигиены workflow-артефактов"
type: method
domain: digital-platform
pack_refs: []
trust: medium
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "WP-358 Ф10 — scripts/check-open-sessions.sh (commit 850c5c2a)"
last_updated: 2026-08-01
---

# DP.M.234 Двухусловное определение «открыто» для гигиены workflow-артефактов

## Описание

Гигиена workflow-артефактов (peer-сессии, draft-PR, inbox-items, todos) требует двухусловного определения «открыто»:

1. **Незавершённое** — `status != completed`. Очевидный кейс.
2. **Завершённое-но-зависшее** — `completed AND age ≥ TTL`. Менее очевидный, типично пропускается.

Без второго условия артефакты накапливаются в active-folder после физического завершения, замусоривая навигацию.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Гигиена навигации ↔ оперативное окно post-completion | Короткий TTL подчищает дашборд, но заставляет пилота успеть заархивировать/сослаться до того, как артефакт исчезнет из открытого списка; длинный TTL даёт воздух для завершения, но накапливает stale items |
| Простота фильтра `status != completed` ↔ полнота картины «что ещё открыто» | Бинарный статус легко запросить и объяснить, но молчит о completed-зависших; добавление второго дизъюнкта ловит edge-case, но усложняет фильтр и требует калибровки TTL |

## Формула

```
open = (status != completed) OR (status == completed AND age ≥ TTL)
```

## Выбор TTL

Баланс между «дать пилоту разумное окно для post-completion действий (архивация, ссылки)» и «не дать застряткам копиться». Эмпирический baseline: 24ч для коротких артефактов (peer-сессия, todo), 7d для PR, 48ч для inbox-items.

## Smoke-test чеклист

- `processing` → показан в open-list (case 1).
- `completed-young` (age < TTL) → не показан (case 2 не сработал).
- `completed-stale` (age ≥ TTL) → показан (case 2 сработал).

## Симметричные применения

- PR в draft >7d → видим в дашборде stale-PR.
- Inbox-items без status >48ч → видим в hygiene-отчёте.
- Todos completed но не archived >24h → видим в day-close cleanup.

## Тест применимости

«Есть ли в системе completed-но-видимое состояние с estimated short lifetime?»
- Да → нужен второй disjunct в open-фильтре с разумным TTL.
- Нет → достаточно `status != completed`.

## Связи

- Источник: WP-358 Ф10 (commit 850c5c2a)
- DP.M.233 (Cutover-date vs backfill) — родственный detector deployment pattern
