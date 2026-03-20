---
id: DP.SC.019
name: Автономная работа IWE (Cloud Runtime)
type: sc
status: draft
layer: L4-Personal
summary: "IWE работает 24/7 в облаке: ночная автоматика, мультиустройственный доступ, управление через Telegram"
consumer: Пользователь IWE (L4-Personal)
created: 2026-03-20
updated: 2026-03-20
related:
  realizes: [S61, S62]
  uses: [DP.ARCH.001]
  extends: [DP.SC.007, DP.SC.107]
  source: "WP-138"
---

# DP.SC.019 — Автономная работа IWE (Cloud Runtime)

## Обещание

**Кому:** Пользователю IWE (любой, кто развернул шаблон FMT-exocortex-template)

**Зачем:** IWE привязана к одному компьютеру. Когда Mac спит/выключен — ночная автоматика (reindex, note-review, strategist) не работает. Нет доступа к планам и заметкам с телефона. Потеря Mac = потеря доступа к IWE.

**Что получит:**
- Автоматика IWE работает 24/7 независимо от состояния компьютера
- Просмотр планов и добавление заметок через Telegram с любого устройства
- Синхронизация cloud ↔ local через git (source of truth не меняется)
- Отказоустойчивость: Mac потерян — IWE продолжает работать

**Критерий приёмки:**
1. Ночная автоматика (scheduler) отрабатывает при выключенном Mac ≥7 дней подряд без сбоев
2. Через Telegram: `/plan` показывает DayPlan, `/note <текст>` создаёт заметку — задержка <3 сек
3. Утром при включении Mac: `git pull` получает все изменения от cloud runtime

## Архитектура: два уровня

### Базовый (для всех пользователей шаблона)

```
GitHub Actions (cron) → bash-скрипты (без LLM)
  ├── reindex.sh — переиндексация knowledge-mcp
  ├── template-sync.sh — синхронизация с шаблоном
  ├── update.sh — Pack→DS propagation
  ├── backup — memory/, CLAUDE.md → git
  └── $0/мес (бесплатный tier GitHub Actions)
```

Не требует Claude API. Обычные bash-скрипты в GitHub Actions по расписанию.

### Продвинутый (opt-in, требует Claude API key)

```
VPS (Hetzner/Fly.io) → Claude Agent SDK
  ├── strategist morning — генерация DayPlan (Claude API)
  ├── note-review — ревью заметок (Claude API)
  ├── week-review — итоги недели (Claude API)
  ├── Telegram Gateway — /plan, /note, /status
  └── ~$5/мес (VPS) + ~$30-90/мес (Claude API, Haiku)
```

Требует Claude API key. Claude Agent SDK с полным tool loop.

### Разделение задач (что требует LLM, а что нет)

| Задача | Нужен Claude? | Уровень | Стоимость |
|--------|:---:|---------|-----------|
| reindex.sh | Нет | Базовый | $0 |
| template-sync.sh | Нет | Базовый | $0 |
| update.sh (propagation) | Нет | Базовый | $0 |
| backup (memory, CLAUDE.md) | Нет | Базовый | $0 |
| extractor inbox-check | Нет | Базовый | $0 |
| **strategist morning** | **Да** | Продвинутый | ~$1-3/день |
| **note-review** | **Да** | Продвинутый | ~$0.5-1/день |
| **week-review (пн)** | **Да** | Продвинутый | ~$1-3/неделю |

## Реализующие сервисы (MAP.002)

| Сервис | Название | Роль | Вход | Выход | Триггер |
|--------|----------|------|------|-------|---------|
| S45 | Cloud Scheduler | R8 Синхронизатор | Расписание + конфиг | Запуск задач | ⏰ cron (GitHub Actions / VPS) |
| S46 | IWE Telegram Gateway | R8 Синхронизатор | Команда пользователя | Ответ (план, статус, заметка) | ⚡ Telegram-команда |

## Пользовательский путь

> Пользователь разворачивает IWE из шаблона. Базовый cloud runtime включается автоматически (GitHub Actions). Продвинутый — по желанию (deploy.sh на VPS).

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Fork FMT-exocortex-template → настроить GitHub Secrets (ANTHROPIC_API_KEY — опционально) | Пользователь | — |
| 2 | GitHub Actions cron запускает bash-скрипты по расписанию (reindex, sync, backup) | S45 Cloud Scheduler | GitHub Actions |
| 3 | Утром пользователь включает Mac, делает `git pull` — получает результаты ночной работы | Пользователь | — |
| 4 | (Продвинутый) `deploy.sh` на VPS — разворачивает полный cloud runtime с Claude Agent SDK | Пользователь | — |
| 5 | (Продвинутый) Strategist генерирует DayPlan ночью через Claude API | S45 Cloud Scheduler | Claude Agent SDK |
| 6 | (Продвинутый) Через Telegram: `/plan` — просмотр, `/note текст` — заметка | Пользователь | S46 TG Gateway |

### Пример: Telegram-команды

```
Пользователь: /plan
Бот: 📋 DayPlan 20 марта 2026 (Пятница)

| # | РП | Статус |
|---|-----|--------|
| 77/78 | Саморазвитие | pending |
| 129 | Мультиканальная публикация | in_progress |
| 138 | Cloud runtime | in_progress |

⏱ Бюджет: ~4h осталось

Пользователь: /note проверить интеграцию calendar-mcp с cloud runtime
Бот: ✅ Заметка записана в inbox/fleeting-notes.md
```

## Связь с другими обещаниями

- Расширяет **[DP.SC.007]** Автоматический ОРЗ-цикл — добавляет cloud execution
- Расширяет **[DP.SC.107]** Мультиповерхностный доступ — добавляет Telegram + mobile
- Потребляет **S17** Scheduler Dispatch (та же логика, другой runtime)
- Питает **[DP.SC.017]** Адаптивное задание на день (DayPlan из облака)

## Фазы реализации

| Фаза | Что | Для кого | Зависимости | Бюджет |
|------|-----|----------|-------------|--------|
| **Ф0** | GitHub Actions workflows для bash-скриптов | Все (шаблон) | — | 4-6h |
| **Ф1** | VPS deploy.sh + Claude Agent SDK strategist | Продвинутые | Claude API key | 8-12h |
| **Ф2** | Telegram Gateway (/plan, /note, /status) | Продвинутые | Ф1 + Telegram bot token | 8-12h |
| **Ф3** | PWA Dashboard | Продвинутые | Ф1 | 20-40h |
