---
id: DP.MAP.002
name: IWE Service Catalog
type: map
status: draft
summary: "Кросс-системный каталог всех сервисов IWE: сервис → роль → вход → выход → потребитель → исполнитель → триггер"
created: 2026-02-23
updated: 2026-02-23
related:
  uses: [DP.AGENT.001, DP.D.033, DP.ARCH.001]
  extends: [DP.MAP.001]
---

# [DP.MAP.002] IWE Service Catalog

> **Назначение:** Единая плоская таблица всех описаний методов (MethodDescription) IWE.
> Каждая строка = одно описание метода: интерфейс (вход → выход) + роль + исполнитель + триггер.
> Первичный ключ — имя. Все ссылки ведут на source-of-truth.
>
> **Терминология (FPF A.3.2):**
> Точный термин: **описание метода** (U.MethodDescription) — спецификация «как делать».
> Житейские имена: «процесс», «сервис» — полисемичны (FPF L-PROC, L-SERV):
>
> | Житейское слово | В этой таблице значит | Но может значить и |
> |-----------------|----------------------|--------------------|
> | «процесс» | Описание метода (рецепт/SOP) | Работу (что произошло) или абстрактный Метод |
> | «сервис» | Описание метода (точка входа) | Обещание потребителю или систему-исполнителя |
>
> При чтении этой таблицы «процесс» и «сервис» = описание метода. В других контекстах — уточняй.
>
> **Правило:** Данные описываются ТАМ, ГДЕ рождаются (method card / role description).
> Эта карта — навигация, не source-of-truth данных.

## Условные обозначения

| Символ | Значение |
|--------|----------|
| ⏰ | По расписанию (cron / scheduler) |
| ⚡ | По событию (event-driven) |
| 👤 | По запросу пользователя (on-demand) |

## Ссылки на source-of-truth

| Ресурс | Путь |
|--------|------|
| Каталог ролей | [DP.AGENT.001 §3.2](../02-domain-entities/DP.AGENT.001-ai-agents.md#32-каталог-ролей-платформы) |
| Реестр исполнителей | [DP.AGENT.001 §3.1](../02-domain-entities/DP.AGENT.001-ai-agents.md#31-реестр-исполнителей) |
| PROCESSES: Стратег | [strategist/PROCESSES.md](../../../../DS-IT-systems/DS-ai-systems/strategist/PROCESSES.md) |
| PROCESSES: Экстрактор | [extractor/PROCESSES.md](../../../../DS-IT-systems/DS-ai-systems/extractor/PROCESSES.md) |
| PROCESSES: Синхронизатор | [synchronizer/PROCESSES.md](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| PROCESSES: Шаблонизатор | [setup/PROCESSES.md](../../../../DS-IT-systems/DS-ai-systems/setup/PROCESSES.md) |
| PROCESSES: Бот | [aist_bot/PROCESSES.md](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| Сценарии Стратега | [DP.AGENT.012/scenarios/](../02-domain-entities/DP.AGENT.012-strategist/scenarios/) |

---

## Каталог сервисов

| # | Сервис | [Роль](../02-domain-entities/DP.AGENT.001-ai-agents.md#32-каталог-ролей-платформы) | Вход | Выход (РП) | Кому | [Исполнитель](../02-domain-entities/DP.AGENT.001-ai-agents.md#31-реестр-исполнителей) | Триггер | Реализация |
|---|--------|------|------|------------|------|-------------|---------|------------|
| S01 | **Day Plan** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | WeekPlan, inbox-triage, вчерашний DayPlan, коммиты | DayPlan | R14 Заказчик, R20 Рецензент | A1 Claude → [strategist.sh](../../../../DS-IT-systems/DS-ai-systems/strategist/scripts/strategist.sh) | ⏰ ежедн 04:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/strategist/PROCESSES.md) |
| S02 | **Session Prep** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | WeekReport, inbox, MAPSTRATEGIC всех систем | WeekPlan (draft) | R14 Заказчик | A1 Claude → strategist.sh | ⏰ Пн 04:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/strategist/PROCESSES.md) |
| S03 | **Week Review** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Коммиты за неделю, DayPlans, WeekPlan | WeekReport + пост итогов | R21 Публикатор, R14 Заказчик | A1 Claude → strategist.sh | ⏰ Пн 00:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/strategist/PROCESSES.md) |
| S04 | **Note Review** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | [fleeting-notes.md](../../../../DS-my-strategy/inbox/fleeting-notes.md), [captures.md](../../../../DS-my-strategy/inbox/captures.md) | Notes-Archive, WP-proposals, captures | R1 (session-prep) | A1 Claude → strategist.sh | ⏰ ежедн 23:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/strategist/PROCESSES.md) |
| S05 | **Evening Review** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | DayPlan, коммиты за сегодня | Итог дня, carry-over | R14 Заказчик | A1 Claude (CLI) | 👤 по запросу | [SC.05](../02-domain-entities/DP.AGENT.012-strategist/scenarios/on-demand/) |
| S06 | **Check Plan** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Текущая задача, WeekPlan/DayPlan | in-plan / aligned / unplanned | R14 Заказчик | A1 Claude (CLI) | 👤 по запросу | [SC.06](../02-domain-entities/DP.AGENT.012-strategist/scenarios/on-demand/) |
| S07 | **Update Priorities** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Изменение приоритетов | Обновлённый Week/DayPlan | R14 Заказчик | A1 Claude (CLI) | 👤 по запросу | [SC.07](../02-domain-entities/DP.AGENT.012-strategist/scenarios/on-demand/) |
| S08 | **Add Workproduct** | [R1 Стратег](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Описание нового РП | Строка в WeekPlan | R14 Заказчик | A1 Claude (CLI) | 👤 по запросу | [SC.08](../02-domain-entities/DP.AGENT.012-strategist/scenarios/on-demand/) |
| S09 | **Knowledge Extraction** | [R2 Экстрактор](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | captures.md, сессионные артефакты | Pack-сущности (M/D/FM/WP/SOTA) | Pack-репо | A1 Claude → [extractor.sh](../../../../DS-IT-systems/DS-ai-systems/extractor/scripts/extractor.sh) | ⏰ ежедн 04:00 / 👤 close | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/extractor/PROCESSES.md) |
| S10 | **Inbox Check** | [R2 Экстрактор](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | captures.md, fleeting-notes.md | Классификация + маршрутизация | R1 Стратег, Pack-репо | A1 Claude (CLI) | 👤 по запросу | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/extractor/PROCESSES.md) |
| S11 | **Ontology Sync** | [R2 Экстрактор](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Pack entities | Обновлённая онтология (SPF.SPEC.002) | I4 knowledge-mcp | A1 Claude (CLI) | 👤 по запросу | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/extractor/PROCESSES.md) |
| S12 | **Q&A** | [R3 Консультант](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Вопрос ученика + knowledge-mcp + DT | Ответ (TG message) | R16 Ученик | A1 Haiku → I1 Bot | ⚡ вопрос ученика | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S13 | **DZ-Check** | [R3 Консультант](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | ДЗ ученика + эталон + DT | Оценка + обратная связь | R16 Ученик | A1 Haiku → I1 Bot | ⚡ ДЗ отправлено | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S14 | **Content Pre-Gen** | [R3 Консультант](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Расписание + knowledge-mcp | Pre-gen cache | R3 (для доставки) | A1 Haiku → I1 Bot | ⏰ scheduler (за 3ч) | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S15 | **Feed Delivery** | [R3 Консультант](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Pre-gen cache + user tier | TG сообщение ленты | R16 Ученик | I1 Bot | ⏰ scheduler | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S16 | **Marathon Step** | [R3 Консультант](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | Marathon config + DT | TG сообщение марафона | R16 Ученик | I1 Bot | ⏰ scheduler | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S17 | **Scheduler Dispatch** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Текущее время + конфигурация | Запуск агентов | R1/R2/R8/R9/R10/R21 | I5 launchd → [scheduler.sh](../../../../DS-IT-systems/DS-ai-systems/synchronizer/scripts/scheduler.sh) | ⏰ 10x/день | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S18 | **Code Scan** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | git repos (коммиты за 24ч) | TG notification + report | R14 Заказчик | I2 bash → [code-scan.sh](../../../../DS-IT-systems/DS-ai-systems/synchronizer/scripts/code-scan.sh) | ⏰ ежедн 04:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S19 | **Pack Projection** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Pack frontmatter | CQRS YAML projection | I4 knowledge-mcp | I2 bash → [pack-project.sh](../../../../DS-IT-systems/DS-ai-systems/synchronizer/scripts/pack-project.sh) | ⏰ после S18 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S20 | **Consistency Check** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Pack repos, downstream repos | Consistency report | R5 Архитектор | I2 bash / A1 Claude | 👤 по запросу | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S21 | **Unsatisfied Report** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | feedback_triage DB | [unsatisfied-questions.md](../../../../DS-my-strategy/inbox/unsatisfied-questions.md) | R7 Триажёр | I2 bash | ⏰ еженед | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S22 | **Fleeting Notes Sync** | [R8 Синхронизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | TG saved messages (файл) | [fleeting-notes.md](../../../../DS-my-strategy/inbox/fleeting-notes.md) | R1 (note-review) | I5 launchd → fswatch | ⏰ каждые 2 мин | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/synchronizer/PROCESSES.md) |
| S23 | **Template Sync** | [R9 Шаблонизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Platform files (CLAUDE.md, prompts, memory/) | [FMT-exocortex-template](../../../../FMT-exocortex-template/) | R14 Заказчик (шаблон) | I3 bash → [template-sync.sh](../../../../DS-IT-systems/DS-ai-systems/setup/scripts/template-sync.sh) | ⏰ ежедн 04:00 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/setup/PROCESSES.md) |
| S24 | **Template Validation** | [R9 Шаблонизатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | FMT-exocortex-template | Pass/Fail report | R9 Шаблонизатор | I3 bash → [validate-template.sh](../../../../DS-IT-systems/DS-ai-systems/setup/scripts/validate-template.sh) | ⏰ после S23 | [PROCESSES](../../../../DS-IT-systems/DS-ai-systems/setup/PROCESSES.md) |
| S25 | **Daily Scan** | [R21 Публикатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | [DS-Knowledge-Index/docs/](../../../../DS-Knowledge-Index-Tseren/docs/) (status=ready) | Расписание публикаций | R14 Заказчик | I9 → scheduler | ⏰ ежедн 03:00 | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S26 | **Scheduled Publish** | [R21 Публикатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | scheduled_publications DB | Discourse topic + frontmatter update | systemsworld.club, R14 | I1 Bot → Discourse API | ⏰ */30 мин | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S27 | **Manual Publish** | [R21 Публикатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Команда /club publish | Discourse topic + новый график | R14 Заказчик | I1 Bot | 👤 команда | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S28 | **Comment Check** | [R21 Публикатор](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | published_posts DB | TG notification о комментарии | R14 Заказчик | I1 Bot | ⏰ */15 мин | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S29 | **Auto-Triage** | [R7 Триажёр](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | helpful=false / user_comment | feedback_triage DB + TG alert | R14 (alert), R7 (review) | Haiku → I1 Bot | ⚡ unhelpful feedback | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S30 | **Triage Session** | [R7 Триажёр](../02-domain-entities/DP.AGENT.001-ai-agents.md#агентские-роли-grade-2) | feedback_triage DB + inbox | Приоритизированный backlog | R6 Кодировщик, R14 | A1 Claude (CLI) | 👤 сессия техдолга | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S31 | **L1 Unstick** | [R11 Наладчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Зависший пользователь (FSM timeout) | FSM reset | R16 Ученик | I1 Bot | ⚡ timeout event | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S32 | **L2 Auto-Fix** | [R11 Наладчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Повторяющаяся ошибка (error_logs) | Автоматический fix | R11 Наладчик | I7 → I1 Bot | ⚡ error pattern | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S33 | **L3 Restart** | [R11 Наладчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Критическая ошибка | Service restart | R11 Наладчик | I7 bash | ⚡ health check fail | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S34 | **L4 Escalate** | [R11 Наладчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Нерешаемая ошибка | GitHub Issue | R6 Кодировщик | A1 Claude → I7 | ⚡ L2 failed 3x | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S35 | **Metrics Collection** | [R10 Статистик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | qa_history, user_profiles, feedback_triage | Агрегированные метрики | R10 (report) | I6 bash | ⏰ ежедн | — |
| S36 | **Analytics Report** | [R10 Статистик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Метрики, тренды | /analytics response | R14 Заказчик | I6 → A1 Claude | 👤 /analytics | — |
| S37 | **Bloom Eval** | [R12 Оценщик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Ответ ученика + эталон | Bloom-оценка (inline) | R3 Консультант | I8 → I1 Bot | ⚡ каждый ответ | [PROCESSES](../../../../DS-IT-systems/aist_bot_newarchitecture/PROCESSES.md) |
| S38 | **WP Validation** | [R12 Оценщик](../02-domain-entities/DP.AGENT.001-ai-agents.md#функциональные-роли-grade-0-со-смешанными-сценариями) | Pack entity draft | Валидация по SPF | R2 Экстрактор | A1 Claude | 👤 по запросу | — |
| S39 | **Fleeting Note** | [R18 Автор заметок](../02-domain-entities/DP.AGENT.001-ai-agents.md#роли-пользователя-a2--7-ролей) | Мысль / наблюдение | .заметка → fleeting-notes.md | R1 (note-review) | R18 (A2 человек) | 👤 когда угодно | — |
| S40 | **Capture-to-Pack** | [R14 Заказчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#роли-пользователя-a2--7-ролей) | Паттерн / различение / метод | Capture: X → Y | R2 Экстрактор | R14 + A1 Claude | 👤 на рубеже | — |
| S41 | **Strategy Session** | [R17 Стратег-интерактив](../02-domain-entities/DP.AGENT.001-ai-agents.md#роли-пользователя-a2--7-ролей) | Вопрос / неудовлетворённость | Решение, обновлённый план | R1 Стратег | R17 + A1 Claude (CLI) | 👤 по запросу | — |
| S42 | **Morning Check** | [R14 Заказчик](../02-domain-entities/DP.AGENT.001-ai-agents.md#роли-пользователя-a2--7-ролей) | Логи, артефакты за ночь | Статус ночных сервисов | R14 Заказчик | R14 + A1 Claude (CLI) | 👤 ежедн утро | — |

---

## Статистика

| Показатель | Значение |
|------------|----------|
| Всего сервисов | 42 |
| По расписанию (⏰) | 22 |
| По событию (⚡) | 9 |
| По запросу (👤) | 11 |
| Уникальных ролей | 12 |
| Уникальных инструментов | 9 (I1-I9) |
| Систем | 8 |

## Как использовать

1. **При диагностике:** Ctrl+F по имени сервиса → проверить вход → проверить выход → ссылка на PROCESSES.md
2. **При добавлении:** Добавить строку сюда → убедиться, что роль есть в [DP.AGENT.001](../02-domain-entities/DP.AGENT.001-ai-agents.md) → добавить в PROCESSES.md системы
3. **При ArchGate:** Найти затронутые сервисы → оценить coupling через колонку «Кому»

## Связь с другими картами

| Карта | Что показывает | Ссылка |
|-------|---------------|--------|
| DP.MAP.001 | Навигация по Pack-сущностям | [DP.MAP.001.md](DP.MAP.001.md) |
| DP.MAP.002 | **Эта карта** — потоки сервисов IWE | — |
| DP.AGENT.001 | Каталог ролей + исполнители (source-of-truth) | [DP.AGENT.001](../02-domain-entities/DP.AGENT.001-ai-agents.md) |

---

*Создано: 2026-02-23. Обновлять при добавлении/изменении сервисов.*
