---
id: DP.M.010
name: Управление жизненным циклом рабочего продукта
type: method
status: active
summary: "Метод гарантирует консистентность РП-объекта во всех хранилищах IWE на протяжении всего цикла: создание → активная сессия → закрытие → архивация. Единственная роль координации — Регистратор РП (DP.ROLE.037)."
created: 2026-05-08
trust:
  F: 4
  G: domain
  R: 0.90
epistemic_stage: evidence
related:
  uses: [DP.M.008, DP.M.003]
  references: [DP.ARCH.001, DP.D.053]
  realized_by: [DP.SC.033]
  role: DP.ROLE.037
tags: [wp, lifecycle, governance, registry, iwe, consistency]
wp: WP-297
---

# Управление жизненным циклом РП (DP.M.010)

## 1. Жизненный цикл РП

РП (Рабочий продукт) — это артефакт с критериями приёмки. Не описание намерения, не процесс.

**Статусы и переходы:**

```
pending ──► in_progress ──► done ──► archived
   ▲              │             │
   │         blocked          resolved_externally
   └──────────────┘
```

| Статус | Значение | Хранится в |
|--------|---------|-----------|
| `pending` | Запланирован, не начат | inbox/ frontmatter |
| `in_progress` | Активная работа | inbox/ frontmatter |
| `blocked` | Заблокирован (> 2 нед) | inbox/ frontmatter + REGISTRY |
| `done` | Критерии приёмки выполнены | frontmatter → `done` + mv archive/ |
| `archived` | Перемещён в archive/wp-contexts/ | archive/ frontmatter |

**Инвариант жизненного цикла:**
> Статус РП в inbox/frontmatter, WP-REGISTRY.md, MEMORY.md и WeekPlan ВСЕГДА консистентен. Расхождение = drift, требует немедленного ремонта.

## 2. Пять хранилищ РП

Каждый РП существует одновременно в пяти местах. Каждое хранилище имеет своего владельца и роль.

| Хранилище | Файл/место | Owner | Роль |
|-----------|-----------|-------|------|
| **Context** | `DS-my-strategy/inbox/WP-{N}-{slug}.md` | Исполнитель (Claude) | Детальное состояние: фазы, handoff, «Осталось» |
| **Registry** | `DS-my-strategy/docs/WP-REGISTRY.md` | Стратег (R1) | Индекс всех РП: ID, название, статус, бюджет |
| **Week index** | `DS-my-strategy/current/WeekPlan W{N}.md` | Стратег (R1) | Текущие РП с приоритетом и дедлайном |
| **Memory** | `MEMORY.md` → секция «Активные РП» | Агент (Claude) | Sweep-таблица для быстрого доступа в начале сессии |
| **External** | Linear (TSR team) | Стратег (R1) | Внешний трекер: синк по ID |

**Правило обновления:** изменение статуса РП → одновременное обновление всех 5 мест.
**Атомарность:** если одно место обновить нельзя, остальные не обновлять (или пометить drift).

## 3. Инварианты (аксиомы целостности)

**I-1. Один факт — одно место.**
Context file (`inbox/`) — source-of-truth для деталей фаз. Registry — для статуса и индекса. Дублирование деталей = drift.

**I-2. Статус frontmatter = мастер.**
`active-wp-sweep.sh` читает YAML frontmatter (строки `---`). Если статус не обновлён в frontmatter при закрытии — РП остаётся zombie.

**I-3. done → archive немедленно.**
Нельзя держать done-РП в `inbox/`. Каждый Day Close — архивация done-РП в `archive/wp-contexts/`.

**I-4. MEMORY.md — только active.**
Таблица «Активные РП» в MEMORY.md = зеркало inbox/. done-РП удаляются немедленно при закрытии.

**I-5. REGISTRY = единственный источник нумерации.**
Следующий номер РП = max(REGISTRY) + 1. Производные файлы не заводят параллельную нумерацию.

## 4. Диагностика drift

**Drift type A — Zombie WP:** `status: in_progress` в frontmatter, но `✅` в REGISTRY.
→ Симптом: DayPlan показывает закрытые РП.
→ Лечение: `bash archive-done-wp.sh {N}`.
→ Профилактика: `active-wp-sweep.sh` выводит секцию `⚠️ Drift` при обнаружении.

**Drift type B — Orphan WP:** РП в REGISTRY, нет inbox-файла.
→ Симптом: sweep не находит context, REGISTRY показывает active.
→ Лечение: закрыть в REGISTRY (`✅`) + удалить из MEMORY.md.

**Drift type C — MEMORY stale:** РП done/archived, но строка осталась в `### 🔄 Активные РП`.
→ Симптом: MEMORY.md > 200 строк, устаревшие РП в sweep.
→ Лечение: удалить строку из активных при Quick Close.

**Drift type D — Week ghost:** РП закрыт, но строка осталась в WeekPlan как `in_progress`.
→ Лечение: обновить статус при Day Close (шаг 2a).

**Инструмент диагностики:** `bash active-wp-sweep.sh` — секция `⚠️ Drift` показывает Type A.
Ручная проверка: `grep -c "in_progress\|active" DS-my-strategy/inbox/WP-*.md` vs `grep -c "🔄" DS-my-strategy/docs/WP-REGISTRY.md`.

## 5. Процессы (5 операций жизненного цикла)

Полное описание → `DS-my-strategy/docs/PROCESSES-wp-lifecycle.md`.

| Процесс | Триггер | Роль | Исполнитель |
|---------|---------|------|-------------|
| Создание РП | WP Gate Ритуал | Стратег + Регистратор | `wp-new` skill |
| Открытие сессии | Начало работы по РП | Регистратор | `run-protocol open` |
| Закрытие сессии | Quick Close | Регистратор | `protocol-close.md §2` |
| Закрытие РП | Критерии выполнены | Регистратор | `archive-done-wp.sh` |
| Синхронизация | Day Close / обнаружение drift | Регистратор | `active-wp-sweep.sh` |

## 6. Шаблон WP-context файла

Минимальный frontmatter:
```yaml
---
wp: NNN
title: "Название — существительное-артефакт"
status: in_progress  # pending | in_progress | blocked | done
priority: P1–P5
budget: Xh
created: YYYY-MM-DD
last_session: YYYY-MM-DD
related:
  - wp: NNN
    relation: enables | blocks | parent | related
    note: "одна строка"
---
```

Обязательные секции: `## Проблема`, `## Артефакт`, `## Фазы реализации`, `## Осталось`.

Секция «Осталось» — машиночитаемый handoff:
```markdown
## Осталось
**Что пробовали:** ...
**Что узнали:** ...
  → memory: ...
**Что дальше:**
- [ ] следующий шаг
**Следующий шаг:** ...
**Контекст для следующей сессии:** ...
```
