---
id: DP.M.246
title: Content-Debt Triage Inbox с ролевым разделением регистрации и принятия решений
type: method
domains: [content-governance, multi-agent, knowledge-management]
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-31
source: WP-376 (content-cleanup-umbrella), DS-my-strategy
pack_refs:
  - DP.ROLE.039 (Peer Agent — исполнитель регистрации)
---

# DP.M.246 — Content-Debt Triage Inbox с ролевым разделением

## Цель

Накапливать содержательные сигналы (расхождения в Pack, граф понятий, drift руководств) без блокировки текущей работы и без автономных решений агента.

## Ключевой принцип (role-split)

**Агент:** регистрирует сигнал НЕМЕДЛЕННО при обнаружении, во время работы — не batch'ом в конце.
**Пилот:** единственный, кто принимает решение о приоритете и разборе.

## Структура сигнала (минимум полей)

```
added: YYYY-MM-DD
added_by: agent/pilot
source: WP-NNN / session / capture
type: P1 | P2 | P3 | P4
location: файл / строка
question: <один вопрос>
```

## Типизация сигналов

| Тип | Описание | Приоритет |
|-----|----------|-----------|
| P1 | Блокер другого РП (нельзя двигаться без решения) | неделя |
| P2 | Расхождение граф понятий / Pack drift | месяц |
| P3 | v4-lint WARN batch (стилистические нарушения) | квартал |
| P4 | Стилистика / опечатки | по возможности |

## Артефакт

Единый файл `DS-my-strategy/current/content-cleanup-backlog.md` = persistent inbox.
Зонтичный РП (WP-376) = орган периодического разбора пилотом.

## Метрика

P1-сигналов > 5 → добавить WP-376-сессию в следующий WeekPlan.

## Анонс при регистрации

```
Capture: backlog CC-NNN — <one-line описание>
```

## Применимость

Любая система с накопленным содержательным долгом, где LLM-агент-исполнитель не должен автономно приоритизировать исправления (Pack, учебные руководства, онтологии).

## Прецеденты

- DS-my-strategy: content-cleanup-backlog.md с 89 AR.5-сигналами (WP-376, 2026-05-31)

## Связи

- MEMORY.md item #12 — операционное правило (CLAUDE.md-уровень)
- DP.M.242 (AR.5-pack-quality-baseline) — метод измерения baseline для P3-сигналов
