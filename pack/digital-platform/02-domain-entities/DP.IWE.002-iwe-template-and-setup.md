---
id: DP.IWE.002
name: IWE Template & Setup
type: domain-entity
status: draft
summary: "Практическое знание о шаблоне IWE: установка, ежедневная работа (ОРЗ), кастомизация (strategy_day, AUTHOR-ONLY зоны, конфиги), роли, обновление, FAQ. Source-of-truth для бота и MCP."
created: 2026-02-27
trust:
  F: 3
  G: domain
  R: 0.6
epistemic_stage: emerging
related:
  refines: [DP.IWE.001]
  uses: [DP.EXOCORTEX.001, DP.ARCH.002, DP.ROLE.001]
---

# IWE Template & Setup

> **Implementation Note.** §1 (концепция шаблона) — домен. §§2-12 (пререквизиты, установка, CLI, tech stack, pricing, bash scripts, MCP server names, security tools) — почти целиком текущая реализация. Этот файл подлежит глубокому рефакторингу: доменная часть (что такое IWE-шаблон, зачем он нужен) → остаётся в Pack. Конкретная установка → [C2.IT-Platform](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/).

## 1. Что такое шаблон IWE

**Шаблон IWE** ([FMT-exocortex-template](https://github.com/aisystant/FMT-exocortex-template)) — готовая конфигурация интеллектуальной рабочей среды. Форкаете → запускаете setup.sh → работаете. Не нужно программировать.

Шаблон содержит:

| Компонент | Что | Контур |
|-----------|-----|--------|
| CLAUDE.md + memory/ | Экзокортекс: правила, протоколы, память | L3 → L4 |
| Стратег (R1) | ИИ-агент: план дня, план недели, разбор заметок | L3 |
| Экстрактор (R2) | ИИ-агент: извлечение знаний в Pack | L3 |
| Синхронизатор (R8) | Центральный диспетчер: расписание, уведомления | L3 |
| DS-strategy/ | Стратегический хаб: планы, inbox, архив | L4 (создаётся при установке) |
| MCP-подключение | Доступ Claude Code к базе знаний платформы | L2 → L4 |

> **Шаблон (L3) ≠ Ваш экземпляр (L4).** Шаблон обновляется через `update.sh` — ваши данные не затираются. Подробнее о контурах: [DP.IWE.001 §4](DP.IWE.001-intelligent-working-environment.md).

## 2. Пререквизиты и установка

> **Implementation Note.** Конкретные инструменты (Git, Node.js, Claude Code CLI), цены ($20/мес Claude Pro), команды установки (setup.sh), шаги, варианты входа → [C2.IT-Platform / System-Implementations / DP.IWE.002-template-setup-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/DP.IWE.002-template-setup-implementation.md).

**Доменные требования:**
- Среда: macOS, Linux или Windows (WSL)
- Версионирование знаний через Git
- Агентный ИИ-ассистент (читает/пишет файлы, выполняет команды, сохраняет контекст)
- Результат установки: экзокортекс + Стратег + стратегический хаб + MCP-подключение

## 4. Ежедневная работа: протокол ОРЗ

Каждая сессия в Claude Code проходит три стадии:

| Стадия | Что происходит | Триггер |
|--------|---------------|---------|
| **Открытие** | WP Gate: задача в плане? Объявление роли, метода, оценки | Любое задание |
| **Работа** | Выполнение + Capture: «есть знание для записи?» | После согласования |
| **Закрытие** | Commit, push, обновление памяти, backup | Команда «закрывай» |

> **WP Gate** — блокирующая проверка: задача есть в плане недели? Если нет — предлагает добавить. Защищает от незапланированной работы.

> **Capture-to-Pack** — на каждом рубеже (подзадача, паттерн, решение): есть ли знание для записи? Анонс: «Capture: [что] → [куда]».

## 4b. Кастомизация: L3 → L4

Шаблон (L3) даёт платформенные протоколы и агентов. Ваш экземпляр (L4) — настройки и расширения.

### Три механизма кастомизации

| Механизм | Что | Пример |
|----------|-----|--------|
| **Конфиг** | yaml-файл с параметрами поведения | `strategy_day: saturday` в `day-rhythm-config.yaml` |
| **AUTHOR-ONLY зоны** | HTML-маркеры для пользовательских блоков в протоколах | `<!-- AUTHOR-ONLY: мои проверки --> ... <!-- /AUTHOR-ONLY -->` |
| **Плейсхолдеры** | Автоподстановка при setup | `{{WORKSPACE_DIR}}`, `{{GITHUB_USER}}` |

### Конфиг (`day-rhythm-config.yaml`)

```yaml
day_open:
  strategy_day: monday        # Любой день: monday..sunday
  self_dev_slot: 1            # Слот саморазвития (всегда первый)
pomodoro:
  work_minutes: 25            # Настройте под себя
  break_minutes: 5
news:
  enabled: false              # Дайджест новостей (требует WebSearch)
```

Агенты (Стратег, Синхронизатор) читают конфиг при каждом запуске. Изменение конфига = мгновенный эффект без обновления шаблона.

### AUTHOR-ONLY зоны

Позволяют добавлять свои проверки и блоки в платформенные протоколы. При обновлении шаблона (`update.sh`) ваш контент сохраняется.

```markdown
<!-- AUTHOR-ONLY: Проверка моих специфичных систем -->
- **Синхронизация веток проекта:**
  ```bash
  cd ~/IWE/my-project && git log --oneline -3
  ```
<!-- /AUTHOR-ONLY -->
```

В шаблоне на месте пользовательских блоков стоят плейсхолдеры `<!-- YOUR CUSTOM CHECKS HERE -->` — замените на свой контент.

## 5. Три роли в шаблоне

> Полный реестр ролей: [DP.ROLE.001](DP.ROLE.001-platform-roles.md). Здесь — только роли из шаблона.

### 5.1. Стратег (R1)

Планирование и рефлексия. Работает по расписанию (launchd/cron).

| Время | Действие | Результат |
|-------|---------|-----------|
| Утро (strategy_day) | Стратегическая сессия + план недели | WeekPlan (с встроенным планом на день) |
| Утро (остальные дни) | План дня из коммитов вчера | DayPlan |
| Вечер (23:00) | Разбор заметок из Telegram | Классификация заметок |
| Канун strategy_day | Week Review — итоги недели | WeekReport |

> **strategy_day** — день стратегирования, настраивается в `day-rhythm-config.yaml` (по умолчанию: monday). В этот день DayPlan не создаётся — план дня встроен в WeekPlan (секция «План на [день]»).

Ручной запуск: `bash ~/IWE/FMT-exocortex-template/roles/strategist/scripts/strategist.sh day-plan`

### 5.2. Экстрактор (R2)

Извлечение знаний в Pack-репозитории. Всегда предлагает — никогда не пишет без одобрения.

| Сценарий | Когда |
|----------|-------|
| session-close | При закрытии сессии Claude Code |
| on-demand | По запросу пользователя |
| inbox-check | Каждые 3 часа (автоматически) |
| knowledge-audit | Аудит полноты Pack |

Установка: `bash ~/IWE/FMT-exocortex-template/roles/extractor/install.sh`

### 5.3. Синхронизатор (R8)

Центральный диспетчер (bash, не ИИ). Управляет расписанием всех ролей, отправляет уведомления в Telegram.

Установка: `bash ~/IWE/FMT-exocortex-template/roles/synchronizer/install.sh`

> **Рекомендация:** Экстрактор и Синхронизатор ставьте после освоения базового цикла со Стратегом (1-2 недели).

## 6. Память: три слоя экзокортекса

| Слой | Файл | Загрузка | Содержание |
|------|------|----------|------------|
| 1 | MEMORY.md | **Всегда** (авто) | Задачи, РП недели, уроки |
| 2 | CLAUDE.md | **Всегда** (авто) | Правила, протоколы, архитектура |
| 3 | memory/*.md | **По запросу** | Различения, FPF, SOTA, чеклисты |

> Подробнее об архитектуре экзокортекса: [DP.EXOCORTEX.001](DP.EXOCORTEX.001-modular-exocortex.md).

## 7. MCP: доступ к знаниям

MCP (Model Context Protocol) — протокол, через который Claude Code подключается к базе знаний платформы.

| Сервер | Что даёт | Документов |
|--------|---------|-----------|
| **knowledge-mcp** | Поиск по Pack, руководствам, DS | ~5 400 |
| **ddt** | Цифровой двойник ученика (цели, самооценка) | — |

MCP подключается автоматически через `.claude/settings.local.json`. Проверка: откройте Claude Code → попросите «Найди документы про принципы» — Claude должен использовать `knowledge-mcp search`.

### 7.1. Работа со своим Pack

После установки IWE вы подключены к **платформенной** базе знаний (knowledge-mcp). Но ваш собственный Pack — это **ваши** файлы на диске. Как Claude Code работает с ними?

#### Различение: платформенные знания ≠ ваши знания

| Источник | Где | Как Claude находит | MCP нужен? |
|----------|-----|-------------------|-----------|
| Платформенные Pack (aisystant) | Сервер платформы | knowledge-mcp `search` | Да (уже подключён) |
| Ваш Pack | `~/IWE/PACK-{domain}/` | Glob / Grep / Read (напрямую с диска) | Зависит от объёма |

#### Три режима работы с Pack

| Режим | Объём Pack | Что делать | Усилия |
|-------|-----------|-----------|--------|
| **Прямой доступ** | <50 файлов | Указать путь в CLAUDE.md + navigation.md | 10 мин |
| **Индексированный** | 50–100 файлов | + manifest с Entity Index ([SPF.SPEC.003](https://github.com/TserenTserenov/SPF/blob/main/spec/SPF.SPEC.003-pack-scalability.md)) | 30 мин |
| **MCP-сервер** | >100 файлов | Форкнуть [knowledge-mcp](https://github.com/aisystant/knowledge-mcp), указать свои источники | 1–2 часа |

#### Режим 1: Прямой доступ (рекомендуется для старта)

Claude Code **умеет читать любые файлы на вашем компьютере**. MCP для этого не нужен. Достаточно сообщить Claude, где лежит ваш Pack.

**Шаг 1.** Добавьте в `~/IWE/CLAUDE.md` секцию:

```markdown
## Мой Pack
- Домен: [название вашей области]
- Путь: ~/IWE/PACK-{domain}/
- Структура: pack/{domain}/00-pack-manifest.md (оглавление)
```

**Шаг 2.** Добавьте в `memory/navigation.md` маппинг:

```markdown
| Вопрос про... | Смотри в |
|--------------|---------|
| [ваш домен] | ~/IWE/PACK-{domain}/pack/{domain}/ |
```

**Результат:** Claude Code при вопросах про ваш домен будет искать по нужным файлам через Glob и Grep. Для Pack из 10–40 файлов этого достаточно.

#### Режим 2: Индексированный доступ

Когда файлов >50, Claude тратит много шагов на поиск нужного. Решение — **manifest с Entity Index** (оглавление всех сущностей с однострочным summary).

**Шаг 1.** Убедитесь, что `00-pack-manifest.md` содержит Entity Index:

```markdown
## Entity Index
| ID | Name | Kind | Summary | Status |
|----|------|------|---------|--------|
| MY.D.001 | ... | D | Одно предложение | active |
| MY.M.001 | ... | M | Одно предложение | active |
```

**Шаг 2.** Claude сначала читает manifest → по summary находит нужную сущность → загружает конкретный файл.

> Подробнее о 3-слойной загрузке Pack: [SPF.SPEC.003](https://github.com/TserenTserenov/SPF/blob/main/spec/SPF.SPEC.003-pack-scalability.md).

#### Режим 3: Свой MCP-сервер

При >100 файлах ручной поиск неэффективен. Семантический поиск (MCP) находит нужное за один запрос.

1. Форкните [knowledge-mcp](https://github.com/aisystant/knowledge-mcp) (open source)
2. В конфигурации укажите путь к вашим Pack-репо как источник
3. Разверните на Cloudflare Workers (бесплатный тир) или локально
4. Добавьте в `.claude/settings.local.json`:

```json
{
  "mcpServers": {
    "my-knowledge": {
      "url": "https://your-worker.workers.dev/sse"
    }
  }
}
```

#### Когда переходить на следующий режим

| Сигнал | Что делать |
|--------|-----------|
| Claude часто «не находит» нужный файл в Pack | Проверьте navigation.md → обновите маппинг |
| Claude делает >3 шагов поиска перед ответом | Добавьте Entity Index в manifest (режим 2) |
| Pack >100 файлов или >2 Pack-репо | Поднимите свой MCP-сервер (режим 3) |

## 8. Telegram-бот @aist_me_bot

Бот — мобильный вход в IWE. Не заменяет Claude Code, но дополняет.

| Функция | Как |
|---------|-----|
| Заметки | `.Текст заметки` (точка + текст) |
| Вопросы | Просто напишите вопрос |
| План дня | `/plan` |
| Тест по теме | `/test` |
| Цифровой двойник | `/twin` |

Заметки попадают в `DS-strategy/inbox/fleeting-notes.md`. Стратег разбирает их вечером.

## 9. Обновление IWE

> **Implementation Note.** update.sh (5 шагов), режимы (--check, --dry-run), TG-оповещения — текущая реализация → [C2.IT-Platform / System-Implementations / DP.IWE.002-template-setup-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/DP.IWE.002-template-setup-implementation.md).

**Доменные принципы:**
- Обновления затрагивают только platform-space (шаблон, протоколы, агенты)
- User-space (MEMORY.md, DS-strategy, Pack, личные настройки) НЕ затрагивается
- Накопительная модель: изменения копятся, одна команда применяет всё

## 10. Безопасность и приватность

> **Implementation Note.** Конкретные сервисы (Anthropic API, WakaTime, GitHub), рекомендации (FileVault, SIP) → [C2.IT-Platform / System-Implementations / DP.IWE.002-template-setup-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/DP.IWE.002-template-setup-implementation.md).

**Доменные принципы:**
- CLAUDE.md, memory/ — только локально (в контекст Claude)
- DS-strategy — приватный репо
- API-ключи — не в коде

## 11. Частые вопросы (FAQ)

> **Архитектура FAQ:** Доменные вопросы («что», «зачем», «как устроено») — здесь (source-of-truth для бота и MCP). Практические вопросы («как сделать», «что делать если») — [LEARNING-PATH §11](https://github.com/aisystant/FMT-exocortex-template/blob/main/docs/LEARNING-PATH.md#11-быстрый-справочник). Краткие выжимки для GitHub-посетителей — README §FAQ.
> **Пополнение:** При появлении нового вопроса → определи тип (доменный/практический) → запиши в соответствующее место → `selective-reindex exocortex-template-docs`.

### Пререквизиты и стоимость

**Нужна ли подписка Anthropic?**
Да. Claude Code требует подписку. Начните с **Claude Pro** ($20/мес). При значительном эффекте и упоре в лимиты — **Claude Max** (~$100/мес).

**Подойдут ли чат-боты (Qwen, Perplexity, routerai.ru)?**
Нет. Чат-боты и поисковые помощники не умеют работать с файлами на компьютере пользователя. Экзокортекс требует **агентного ИИ-ассистента** — читает/пишет файлы, выполняет команды, сохраняет контекст между сессиями.

**Какие альтернативы Claude Code?**

| Альтернатива | Тип | Цена | Модели |
|---|---|---|---|
| **Cursor** | IDE | от $20/мес | Claude, GPT, свои |
| **GitHub Copilot** (Agent mode) | VS Code extension | от $10/мес | Claude, GPT |
| **Cline / Roo Code** | VS Code extension (open source) | Бесплатно + API-ключ | Любые |
| **Aider** | CLI (open source) | Бесплатно + API-ключ | Любые |

**Требования к модели:** Экзокортекс требует от LLM сложного агентного поведения (многошаговые протоколы, одновременная работа с 5-10 файлами, надёжное редактирование). Рекомендуемые: Claude Opus/Sonnet, GPT-4o/o1, Gemini 2.5 Pro. Модели слабее (Qwen, Llama, Mistral) — ненадёжны для экзокортекса, хотя подходят для обычного кодинга.

**Оптимизация стоимости (Claude):** Выбор модели под задачу экономит лимит подписки. Opus — архитектура, стратегия, сложный код. Sonnet — типовые задачи, контент, одно-файловые правки. Haiku — поиски, автозадачи (Стратег, Экстрактор), тривиальные фиксы (~80% экономии vs Opus). Переключение: `/model` в Claude Code.

### Платформа и совместимость

**Работает ли на Windows?**
Через WSL (Windows Subsystem for Linux) — да. Launchd не работает в WSL — используйте cron.

**Нужно ли программировать?**
Нет. Шаблон — готовая конфигурация. Установка через setup.sh. Работа — через Claude Code на естественном языке.

### Компоненты и архитектура

**Можно ли без Стратега?**
Да. Claude Code + CLAUDE.md + memory/ работают полностью. Стратег — автоматизация планирования. Без него планируете вручную.

**Что такое Pack?**
Pack — предметная база знаний (source-of-truth для домена). Создаётся позже, когда накопите captures. Первый шаг — работа с `captures.md` через Экстрактор. Как подключить свой Pack к Claude Code — см. §7.1.

**Нужен ли MCP для моего Pack?**
Не обязательно. При <50 файлах Claude Code читает Pack напрямую с диска — достаточно указать путь в CLAUDE.md. MCP нужен при >100 файлах или нескольких Pack-репо. Подробнее — §7.1.

**Как проверить MCP?**
Откройте Claude Code в папке экзокортекса → попросите «Найди документы про принципы». Claude должен использовать `knowledge-mcp search` и вернуть результаты.

### Кастомизация

**Как сменить день стратегирования?**
В `memory/day-rhythm-config.yaml` измените `strategy_day: monday` на нужный день (monday..sunday). Стратег, Синхронизатор и протокол Day Open читают это значение при каждом запуске.

**Как добавить свои проверки в протоколы?**
Используйте AUTHOR-ONLY зоны: оберните свой блок маркерами `<!-- AUTHOR-ONLY: описание --> ... <!-- /AUTHOR-ONLY -->`. При обновлении шаблона ваш контент сохранится. Подробнее — §4b.

### Установка и устранение проблем

**Клонирование попало в ~ вместо ~/IWE**
Команды установки нужно выполнять **в одном терминале** последовательно. Если между `cd ~/IWE` и `gh repo fork ...` вы открыли новый терминал — он начинает из домашней директории (`~`), и clone уходит не туда. Решение: удалите папку из `~` и повторите все команды из одного блока, начиная с `cd ~/IWE`.

## 12. Устранение проблем

> **Implementation Note.** Конкретные команды диагностики → [C2.IT-Platform / System-Implementations / DP.IWE.002-template-setup-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/DP.IWE.002-template-setup-implementation.md).

## 13. Первая неделя

> **Implementation Note.** Конкретный чек-лист по дням → [C2.IT-Platform / System-Implementations / DP.IWE.002-template-setup-implementation.md](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/DP.IWE.002-template-setup-implementation.md).

**Доменный принцип:** Первая неделя: установка → заполнение памяти → первая сессия ОРЗ → подключение бота → Week Review.

## 14. Связанные документы

- [DP.IWE.001](DP.IWE.001-intelligent-working-environment.md) — что такое IWE, зачем, архитектура (5 видов: системы, описания, роли, методы, рабочие продукты), тиры, контуры
- [DP.EXOCORTEX.001](DP.EXOCORTEX.001-modular-exocortex.md) — архитектура экзокортекса: 3 слоя, модульность, template-sync
- [DP.ARCH.002](DP.ARCH.002-service-tiers.md) — 4-осевая модель тиров: T0-T4 + TM/TA/TD
- [DP.ROLE.001](DP.ROLE.001-platform-roles.md) — полный реестр ИИ-ролей
- [SETUP-GUIDE.md](https://github.com/aisystant/FMT-exocortex-template/blob/main/docs/SETUP-GUIDE.md) — пошаговая инструкция (downstream)
- [LEARNING-PATH.md](https://github.com/aisystant/FMT-exocortex-template/blob/main/docs/LEARNING-PATH.md) — полный путь изучения IWE
- [SPF.SPEC.003](https://github.com/TserenTserenov/SPF/blob/main/spec/SPF.SPEC.003-pack-scalability.md) — масштабируемость Pack: 3-слойная загрузка, MCP-сервер, Sub-Pack
