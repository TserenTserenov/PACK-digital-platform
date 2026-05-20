---
id: DP.MAP.001
name: Pack Navigation Map
scope: full-pack
created: 2026-05-20
last_updated: 2026-05-20
generated: true
---

# [DP.MAP.001] Pack Navigation Map

> Auto-generated from frontmatter on 2026-05-20. Do not edit manually.

---

## Statistics

| Kind | Count |
|------|-------|
| AISYS (AISYS) | 4 |
| ARCH (ARCH) | 9 |
| ASSIST (ASSIST) | 1 |
| CONCEPT (CONCEPT) | 3 |
| Distinctions (D) | 41 |
| ECON (ECON) | 1 |
| EXOCORTEX (EXOCORTEX) | 1 |
| Failure Modes (FM) | 50 |
| IWE (IWE) | 7 |
| KR (KR) | 1 |
| Methods (M) | 107 |
| Maps (MAP) | 2 |
| METHOD (METHOD) | 8 |
| NAV (NAV) | 1 |
| ONT (ONT) | 1 |
| ORG (ORG) | 1 |
| ROADMAP (ROADMAP) | 2 |
| ROLE (ROLE) | 37 |
| RUNBOOK (RUNBOOK) | 1 |
| SC (SC) | 94 |
| SoTA Annotations (SOTA) | 26 |
| SYS (SYS) | 1 |
| Work Products (WP) | 16 |
| **Total** | **415** |

## Distinctions

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.D.025 | Harness ≠ Agent | Harness (упряжь/обвязка) определяет результат больше, чем мощность агента/модели | active |
| DP.D.027 | Content Budget Model (3 оси) | Длина, глубина и персонализация контента — три независимые оси, управляемые раздельно | active |
| DP.D.028 | User Data Tiers — тирование данных пользователя | Данные пользователя растут с тиром платформы: T0 без Ory (telegram_id) → T1 с Ory (UUID) → T2 минимальная персонализация (профиль + история) → T3 цифровой двойник (ИИ знает контекст) → T4 личный контекст + ИИ-агенты (со-мыслитель). Ортогональные оси: TM (наставник), TA (администратор), TD (разработчик) | active |
| DP.D.029 | Language Model ≠ World Model | LLM = пассивные знания о мире из текстов (кабинетный учёный). World Model = активная модель, обновляемая из взаимодействия с реальностью (инженер). Критерий: замыкает ли система цикл действие-измерение-обновление | active |
| DP.D.030 | Топология деплоя платформы | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.031 | MCP Access Model: публичный vs приватный | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.032 | Единый Circuit Breaker для внешних зависимостей | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.033 | Role-Centric Architecture (Ролецентричная архитектура) | Роль описывается независимо от исполнителя. Исполнитель выбирается и подготавливается отдельно. Роль = маска, которую надевает система (сама — если агент, или по воле другого агента — если инструмент). Одно имя (например, 'Синхронизатор') может обозначать и роль, и систему-исполнителя — это разные ракурсы, не тождество. | active |
| DP.D.034 | Three-Axis Access Control Model (Трёхосевая модель доступов) | Доступ на платформе определяется тремя ортогональными осями: Entitlement (тир — что доступно по подписке), Role (роль — что можно делать), Scope (область видимости — над чем). Permission = Entitlement × Role × Scope. Устраняет необходимость в подролях (Администратор-1, Администратор-2) — это одна роль с разным scope. | active |
| DP.D.035 | Data Policy — политика данных IWE | Единая политика данных платформы: что собирается, где хранится, кому доступно, как удалить. Принятие — при установке шаблона (setup.sh). Агрегирует DP.D.028, DP.D.031, DP.ARCH.005, DP.ARCH.006, DP.ARCH.007 | active |
| DP.D.036 | BYOB Knowledge Architecture | Различение BYOB (Bring Your Own Backend) vs Managed: данные пользователя хранятся на его ресурсах, платформа даёт код и L2-знания. Связано с MCP Hub (ADR-018 v2) и контурами L2/L4. | draft |
| DP.D.037 | Рабочий продукт как инструмент связи | РП — не красиво оформленные данные, а инструмент, показывающий связь между элементами и работающий на достижение миссии | active |
| DP.D.040 | Мировоззрение → Pack: аналогия художника | Художник кодирует мировоззрение в произведение. Профессионал кодирует доменное знание в Pack. Оба трансформируют внутреннее в описание | active |
| DP.D.050 | Роли Созидателя | 5 ролей Созидателя (Ученик, Интеллектуал, Профессионал, Исследователь, Просветитель). Каждый человек выполняет все 5 одновременно. Внутри каждой роли — ступени мастерства. Основа траектории персонального развития. | active |
| DP.D.052 | Различение: Персона / Память / Контекст | Три слоя пользовательской модели — замена legacy-термина «ЦД». Критерий разделения = writer + owner (source-of-truth), не когнитивный и не по TTL. Персона = user-owned Git, Память = platform-owned Neon, Контекст (= Проекция) = runtime-ephemeral. | active |
| DP.D.053 | Problem Task Workflow | — | active |
| DP.D.054 | Dashboard Audience Projections | — | active |
| DP.D.055 | Домен vs Тема | — | active |
| DP.D.056 | IWE Слои и портируемость | — | active |
| DP.D.057 | Routing-решение ≠ Обновление карты маршрутизации | — | active |
| DP.D.058 | Service Clause (Обещание) ≠ Carrier (Носитель реализации) | — | active |
| DP.D.059 | Три класса хранения credentials при ротации | — | active |
| DP.D.060 | Entity-БД vs Special-БД: изолированный threat model и независимый lifecycle | — | active |
| DP.D.061 | Neon Db Count Layers | — | active |
| DP.D.062 | Потребитель SC — роль, не канал | — | active |
| DP.D.063 | Платформа-инициированные vs потребитель-инициированные уведомления | — | active |
| DP.D.064 | То же обещание ≠ Другое обещание (scope-дискриминатор при закрытии РП) | — | active |
| DP.D.065 | Ortho-различение: специализация-по-содержимому ≠ атрибут-применимый-к-любому | — | active |
| DP.D.066 | Чертёж (планирующий артефакт) ≠ Стройка (реализационный артефакт) | — | active |
| DP.D.067 | Card ≠ Append-only Event (Aggregate-card vs Event-stream в event sourcing) | — | active |
| DP.D.068 | Discovered-WP vs Discoverer-WP — owner-routing бага из post-hoc audit'а | — | active |
| DP.D.069 | Documentation-WP ≠ Implementation-WP — paired related-WPs, не один РП | — | active |
| DP.D.070 | Артефакт-режим ≠ артефакт | — | active |
| DP.D.071 | Декларированный bounded context ≠ Фактический bounded context | — | active |
| DP.D.072 | Спецификация формата ≠ Чеклист приёмки формата | — | active |
| DP.D.073 | Внешняя витрина ≠ Внутренняя часть платформы (по жизненному циклу + аудитории, не по технослою) | — | active |
| DP.D.074 | Трёхслойная модель MCP в IWE | Три категории MCP в IWE: платформенные (общее знание), персональные (знания пользователя), вендорские (внешние сервисы). Все платформенные — наши сервисы с RLS изоляцией. | draft |
| DP.D.075 | personal_search (семантический транспорт) ≠ Honcho (накопитель инференций) | personal_search — семантический доступ к источникам текста; Honcho — накопитель паттернов между запусками. В cognitive proxy pipeline: personal_search = транспорт, Honcho = память. | draft |
| DP.D.076 | Контролёр развития ≠ Оркестратор / Проводник / Навигатор / Оценщик / Аттестатор / Диагност | Контролёр развития (R46) — плановый фоновый сканер маркеров; не путать с шестью смежными ролями: Оркестратором (родитель), Проводником (FSM в боте), Навигатором (методология), Оценщиком (оценка ответа), Аттестатором (стадия по событиям), Диагностом (cp-профиль по запросу). Все шесть разделены по pace-слою, источнику истины и объекту внимания. | draft |
| DP.D.077 | Interface Onboarding ≠ Learning Onboarding (по объекту обучения: интерфейс vs контент) | — | active |
| DP.D.078 | Ценностный язык ≠ Технический язык (в user-facing копии) | — | active |

## Methods

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.M.001 | Извлечение знаний | Трансформация сырой информации в Pack-совместимые сущности и DS docs/ через обнаружение, классификацию, двойной routing и формализацию | draft |
| DP.M.002 | Применение стратегического DDD | Метод применения стратегического DDD к Pack и экзокортексу: BC mapping, UL extraction, Context Map для inter-agent integration | draft |
| DP.M.003 | Context Engineering Protocol | Метод проектирования контекста ИИ-агента: Write/Select/Compress/Isolate → CLAUDE.md + memory/ + Pack layers | draft |
| DP.M.004 | Адаптивная персонализация по состоянию | Адаптация контента развития (промпты, bloom_level, тематика) на основе состояния пользователя из теста систематичности | draft |
| DP.M.005 | АрхГейт (ArchGate) | Блокирующая оценка архитектурного решения по 7 характеристикам (ЭМОГССБ): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность, безопасность. Без прохождения — решение не принимается | active |
| DP.M.006 | Самопроверка вайб-режима (Vibe-Check) | Метод оценки допустимости вайб-режима работы по 6 характеристикам проектной ситуации. Определяет: вайб допустим или нужна профессиональная работа | draft |
| DP.M.007 | Intervention Loop (Петля интервенции) | Метод замыкания цикла действие-измерение-обновление для AI-агентов на LLM-платформе: зондирование реальности, фиксация невязки, обновление модели. Компенсирует отсутствие world model | draft |
| DP.M.008 | Культура работы IWE (Work Culture) | Культура работы IWE: 14 элементов в трёх разрезах — протоколы (формализованные последовательности), навыки (нарабатываемые по ситуации), форматы (стандарты оформления). #12 ТО = поддержание текущего состояния. #14 Эволюция = развитие системы. Реализация — в DS/FMT, инварианты — здесь | active |
| DP.M.009 | Расширяемость шаблонных систем (Template Extensibility) | Метод проектирования расширяемости в системах с платформенным шаблоном и пользовательскими инстансами. Три паттерна (drop-in, overlay, 3-way merge), критерии выбора, протокол обновления с обнаружением противоречий | draft |
| DP.M.010 | Управление жизненным циклом рабочего продукта | Метод гарантирует консистентность РП-объекта во всех хранилищах IWE на протяжении всего цикла: создание → активная сессия → закрытие → архивация. Единственная роль координации — Регистратор РП (DP.ROLE.037). | active |
| DP.M.011 | Агрегация captures из множества источников | Единый inbox-файл (captures.md) наполняется автоматически из 4 каналов с маркерами источника для идемпотентной обработки Экстрактором | draft |
| DP.M.012 | Machine-Check Postcondition | — | active |
| DP.M.013 | Security Audit Cadence | Метод управления аудитом безопасности платформы через три уровня периодичности: event-driven (каждое архитектурное решение, ~0 ₽), weekly light-check (2 мин, ~0 ₽), daily automated deep-scan (systemd-timer + subagent с context isolation, ~$1.5/день). Архетип применим к любой platform с security-требованиями. | active |
| DP.M.014 | Evaluator Worker | — | draft |
| DP.M.015 | Четырёхслойная каскадная зависимость в activity-based геймификации | — | draft |
| DP.M.016 | Диагностика зрелости домена (3 вопроса) | — | active |
| DP.M.017 | Runtime Tool Discovery через JSON-RPC | LLM-клиент строит список tool в runtime через tools/list JSON-RPC с TTL-кэшем (15 мин) и fallback на last-known-good при недоступности сервера. Hardcoded список tool = антипаттерн. | draft |
| DP.M.018 | External Data Fallback Hierarchy | — | active |
| DP.M.019 | Промоция скрипта из авторского IWE в платформенный шаблон (L3→L1) | 7-шаговый процесс перевода скрипта из авторского IWE (L3) в платформенный шаблон FMT (L1): проверка коллизий, параметризация, smoke-test в 3 кейсах, обновление манифеста, коммит feat: promote. | draft |
| DP.M.020 | Паттерн необязательной зависимости скрипта через params.yaml | Паттерн проектирования shell-скриптов с опциональными внешними зависимостями: ключ в params.yaml с дефолтом '' (пустая строка), graceful skip при пустом значении, warning+exit 1 при несуществующем пути. Три обязательных smoke-кейса. | draft |
| DP.M.021 | GitHub App Platform Integration | — | — |
| DP.M.022 | Cache-safe Personal Dashboard (снапшот + daily sync) | — | — |
| DP.M.023 | Chaining nightly tasks через фиксированный offset | Зависимые ночные задачи (producer → consumer) запускаются с фиксированным N-минутным offset вместо явной зависимости After=/ExecStartPost. Устойчив к задержкам producer'а. | active |
| DP.M.024 | Fallback-поле для NULL в темпоральных расчётах с legacy-данными | — | draft |
| DP.M.025 | Волновое развёртывание (Wave Rollout) | — | draft |
| DP.M.026 | git-fork-push-pattern | — | active |
| DP.M.027 | 12-factor Matrix для инвентаризации production deployment | Метод систематической инвентаризации всех production deployment units через матрицу F1-F12. Позволяет обнаруживать системные дефекты (например, floating deps у всех Python-сервисов) за один проход по стеку. | active |
| DP.M.028 | Stateless Worker — PostgresStorage + CursorCache + batched-flush | — | active |
| DP.M.029 | Cross-verification CRITICAL-флагов автоматического аудита | — | active |
| DP.M.030 | F9 Disposability — двухкомпонентный паттерн worker | Для 12-factor F9 (Disposability) в event-driven workers нужны два независимых механизма: (1) SIGTERM handler для graceful shutdown, (2) cursor-based idempotency для crash safety. Только их комбинация даёт полный F9. | active |
| DP.M.031 | Reusable Flow Export — экспортируемая функция для множественных точек входа | Функция UI-flow (consent, onboarding, активация) оформляется как reusable export из своего модуля, а не как inline-код в одном handler. Позволяет нескольким entry points (deep-link, команда, кнопка, QR-код, UTM-параметр) делегировать единой реализации без дублирования. | active |
| DP.M.032 | Предпочтение MD-формата для плотного LLM-контекста | MD-формат на 26% короче HTML при одинаковой точности распознавания Haiku. Рекомендация: использовать MD для плотного структурированного контекста агента; таблицы — исследовать отдельно. | active |
| DP.M.033 | Matrix-CI по конфигурационному параметру шаблона | CI-пайплайн для шаблонов запускается с матрицей значений ключевого конфигурационного параметра. Немедленно выявляет hardcoded константы, которые не проявляются у автора с дефолтным именем. | active |
| DP.M.034 | ArchGate Operational Backing Check | Метод проверки качества ArchGate-профиля ЭМОГССБ: профиль силён, когда backed операционными данными; слаб, когда строится на paper comparison. 3 диагностических признака слабого профиля + финализирующий вопрос. | active |
| DP.M.035 | Явные триггеры извлечения модуля в сервис | При выборе 'модуль внутри монолита/Worker' вместо 'отдельный микросервис' — немедленно задокументировать измеримые триггеры обратного extraction. 4 типа триггеров. Без явных триггеров решение становится вечным и пропускает правильный момент для review. | active |
| DP.M.036 | Peer Agent Onboarding | — | draft |
| DP.M.037 | Personal Guide Lifecycle | — | draft |
| DP.M.038 | Идемпотентное распределение скиллов при рендере | Паттерн: при каждом рендере персонального руководства агент идемпотентно копирует набор скиллов в .claude/skills/ целевого репо. Идемпотентность: копировать только при отсутствии файла или изменении checksum. Цель: обеспечить channel-parity — доступность скиллов в browser-канале без VS Code. | active |
| DP.M.039 | Manifest Version Release Gate (Проверка версии manifest перед релизом) | Pre-release детектор: версия в manifest.json должна совпадать с версией в CHANGELOG.md. Ловит забытый запуск generate-manifest.sh перед релизом. | draft |
| DP.M.040 | Progress Counter N/M для batch-операций CLI (CLI Batch Progress UX) | Вывод прогресс-строки (N/M) в теле batch-цикла в shell-скриптах предотвращает иллюзию зависания при длинных операциях. Порог: >10 итераций или >5 сек. | draft |
| DP.M.041 | Posttooluse Hook Derived Sync | — | draft |
| DP.M.042 | Platform Audit Multilens | Поэтапная ревизия production-платформы: 12-factor (уровень 0) → SRE/SLO (1) → Well-Architected (2) → Team Topologies (3) → TOGAF (4) → DORA (5) → LLMOps (6) | active |
| DP.M.043 | Жизненный цикл генерируемых артефактов: явный archive-шаг с retention-окном | — | active |
| DP.M.044 | Extractor Yesterday Step | Extractor Yesterday — паттерн замыкания knowledge pipeline: Day Open явно включает просмотр captures экстрактора за вчера как обязательный шаг до начала новой работы. Без этого шага captures попадают в inbox, но не в фокус сессии. | active |
| DP.M.045 | Три оси Service Clause автоматизированного процесса | — | active |
| DP.M.046 | Keyset pagination для projection-worker | — | active |
| DP.M.047 | Стресс-тест бэкапа через restore | — | active |
| DP.M.048 | Дисциплина scope-решений при закрытии РП | Метод определяет, когда смежная работа, обнаруженная при реализации или закрытии РП, должна стать фазой текущего РП, а когда — отдельным РП. Основан на дискриминаторе обещания DP.D.064. | active |
| DP.M.049 | Lean Frontmatter Pilot | Двухфазная схема frontmatter / DSL: фаза 1 (пилот) — минимальный набор полей, фаза 2 (после фиксации структуры) — расширение через миграцию в отдельный artifact (concept-graph YAML, schema-registry). Избегает 2-3 переделок за пилот. | active |
| DP.M.050 | Env I Isolation | — | active |
| DP.M.051 | Spawned Wp From Phase | — | active |
| DP.M.052 | Dt Write Api Browser Channel | — | active |
| DP.M.053 | Pack как SoT нормативов: код = зеркало | — | active |
| DP.M.054 | Targeted backfill via dedicated queue for cursor-workers | — | active |
| DP.M.055 | Config SoT Triplet (Python source + SQL generator + validator) | — | active |
| DP.M.056 | IntegrationGate Applicability Test | Тест применимости IntegrationGate-каркаса (Service Clause → сценарии → роль → реализация) за пределы код-сервисов: применим к любому repeatable workflow с явным потребителем и измеримым инвариантом — документационные конвейеры, курсовые пайплайны, процессы публикации | active |
| DP.M.057 | A/B-оценка альтернативного ML-компонента | — | active |
| DP.M.058 | Гейт создания нового Pack при доменных кандидатах без дома | При knowledge extraction с внешнего источника: универсальные кандидаты → существующие PD-Pack'и сразу; доменные кандидаты без существующего Pack'а — defer all-together как extraction-report до single decision point /pack-new vs /pack-extend. Защищает от fragmentation доменной онтологии по чужим Pack'ам. | active |
| DP.M.059 | Триада артефактов закрытия фазы РП | Закрытие фазы РП ≠ закрытие РП ≠ открытие нового РП. Полнота закрытия фазы достигается коммитом из трёх артефактов: (1) inbox-context update с дельтой artifacts фазы; (2) cross-link на смежные РП при наличии триггеров; (3) side-artifact (extraction-report, decision log) при наличии extraction-работы. Тест полноты — обратимость через 6 месяцев. | active |
| DP.M.060 | Атомарные ВДВ-шаги | — | active |
| DP.M.061 | Детекция bottleneck-shift после устранения tech-блокера | После устранения tech-блокера bottleneck не исчезает, а смещается в operational/usage/поведенческий слой. Без переоценки карты направлений рисуют «зелёное» при низком conversion в целевое поведение. Тест: «N дней после снятия блокера — какие пилоты/users изменили поведение?» Если <50% — новый bottleneck в operational/usage, не tech. Анти-паттерн: продолжать наращивать tech-функционал когда operational gap не закрыт (инфляция Inventory без Throughput). | active |
| DP.M.062 | Bridge-backfill через shared identifier при blocked identity-provider | При cross-system identity-миграции, когда new identity-provider (ORY, OAuth, SSO) недоступен или unblocked-deploy откладывается — не блокировать миграцию полностью. Искать существующий shared identifier (id, present в обеих БД: legacy + new) и проводить linking через него. Покрытие partial + weekly retry для непокрытых. Тест: «есть ли поле, присутствующее в обеих системах?» Да → backfill через него. | active |
| DP.M.063 | Triple-deploy + URL-derived basename для tool promotion | Инструмент, работающий в авторском IWE + FMT-шаблоне (для других пилотов) + DS-репо — требует 3-х синхронизированных копий. Pattern: (1) одна реализация (Python, не bash), (2) три target-локации с симметричными именами, (3) FMT-версия обезличена через `_repo_basename` из git remote URL вместо hardcoded имени. Тест обезличивания: «если установить шаблон в репо с другим именем — скрипт сам подхватит правильное basename?» Да → корректное обезличивание. | active |
| DP.M.064 | Manual smoke + analogous-pattern coverage как substitute полной автоматизации | Когда full-automation smoke заблокирован внешним фактором (scheduling, deploy infrastructure, vendor bug) — DoD фазы можно закрыть не пустым deferral, а зачётом manual smoke + analogous-pattern coverage. Тест применимости: «можно ли доказать, что execution-path работает, через два независимых способа использования, оба не зависящие от заблокированного компонента?» Да → architecture validation done, automation defer как отдельная фаза. | active |
| DP.M.065 | 4 условия легитимации temporal-derivation routing | Routing через изменяемую Карту (routing_key → path) — temporal fallback, по умолчанию FAIL conjunctive screening ЭМОГССБ по Стабильности. НО: при выполнении всех 4 условий одновременно паттерн становится допустимым: (1) нет override; (2) total pure derivation (каждый kind → ровно один target, нет default/wildcard); (3) freeze-at-assignment (path материализуется в task при pending→assigned); (4) раздельная Карта от справочника. Если хотя бы одно не выполнено → temporal fallback → FAIL. | active |
| DP.M.066 | Multi-round verifier с сужающимся scope | — | active |
| DP.M.067 | Two-pass review — subagent + self-revisit | — | active |
| DP.M.068 | Scope-creep corrective quad — 4 действия в один fix-pass | — | active |
| DP.M.069 | Multi-scenario Service Clause — одно обещание, N delivery-сценариев | — | active |
| DP.M.070 | Двухфазный тест гипотезы (baseline → parameterized) | — | active |
| DP.M.071 | Pre-implementation smoke | — | active |
| DP.M.072 | Split-transaction для late-webhook с CHECK constraint | — | active |
| DP.M.073 | Pause-before-fix для воркеров с downstream notifications | — | active |
| DP.M.074 | Provisional payment_id для late-binding payment APIs | — | active |
| DP.M.075 | No-op heartbeat для детекции silent-fail в scheduled workflow | — | active |
| DP.M.076 | Migration flag (default WARN → opt-in FAIL) для постепенной валидации | — | active |
| DP.M.077 | Common-prefix compression в output путей и циклов | — | active |
| DP.M.078 | Многоточечная propagation нового архитектурного правила | — | active |
| DP.M.079 | Pack-watcher cross-repo trigger | Push-trigger из Pack-репо (SoT) в downstream-репо через GitHub Actions repository_dispatch. Заменяет polling-cron на push-on-change. Применим к Pack→curriculum, Pack→personal-guide regen, Pack→reward_rules sync. | emerging |
| DP.M.080 | Composite indicator — взвешенная сумма провайдеров | — | active |
| DP.M.081 | PII Gate через синтетику — bypass для research-фаз | — | active |
| DP.M.082 | WP scope boundary через DP.SC interfaces | — | active |
| DP.M.083 | Batch frontmatter enum-validator (pre-commit) | — | active |
| DP.M.084 | Batch-extraction pipeline из большого корпуса | — | active |
| DP.M.085 | Онбординг пилота: Персональное руководство | — | active |
| DP.M.087 | SECRETS.md как обязательный артефакт перед deploy на новый хост | — | active |
| DP.M.088 | CI + pre-commit как defense-in-depth для Pack-инвариантов | Двухуровневая защита Pack-инвариантов: pre-commit hook = быстрый локальный fail; GitHub Action = серверный enforcement при push/PR. Агентские коммиты (--no-verify, headless) покрываются только CI-слоем. | active |
| DP.M.089 | Ф0-исследование cost baseline перед LLM-оптимизацией | — | draft |
| DP.M.090 | Mutation Testing для CI Enforcement Guards в Pack-репо | — | draft |
| DP.M.091 | Scope Guard — enforcement Parliament-модели через enum + schema isolation | — | active |
| DP.M.092 | Infra Artifact As Create Flow Step | — | active |
| DP.M.093 | CI артефакт встраивается в create-flow, не отдельная задача | — | active |
| DP.M.094 | Dual-signal enforcement gate для ритуального перехода | — | active |
| DP.M.095 | Atomic cross-repo terminology sync | — | active |
| DP.M.096 | Выбор Property Graph vs Triple Store для доменной knowledge base с rich metadata | — | draft |
| DP.M.097 | Completeness Gate: cross-check spec-множества vs impl-множества для детекции пропущенных случаев | — | draft |
| DP.M.098 | Premise pain probe перед архитектурой автоматизации | — | draft |
| DP.M.099 | Illustration as First-Class Pack Object | — | — |
| DP.M.100 | Vocabulary Sufficiency Gate | — | — |
| DP.M.101 | Семантическое версионирование для Docs-as-Code | Алгоритм автоматической классификации bump'ов для docs-as-code: git log от последнего тега → классификация коммитов по паттернам (feat→minor, fix→patch, BREAKING→major) → changelog entry + релиз. Применимо к любому документационному репо с conventional commits. | active |
| DP.M.102 | Условный автоматический merge через метки PR и CI-гейт | PR с разрешённой меткой (hotfix, pilot-approved) + все CI-чеки зелёные → автоматический merge. Создаёт ускоренную полосу для срочных исправлений без обхода CI. Граница безопасности: только разрешённые labels + CI pass обязателен. | active |
| DP.M.103 | Жизненный цикл создания доменного Pack (7 фаз) | Полный lifecycle создания нового Pack: Ф1 (онтология + SOTA) → Ф2 (различения) → Ф3.5 (extraction из корпуса) → Ф4 (IntegrationGate) → Ф5 (batch mining) → Ф7 (MAP + CHANGELOG + README + SPF 09-11). IntegrationGate до extraction = правильный порядок. SPF 09-11 = обязательное завершение. | active |
| DP.M.104 | Cross-repo publication pipeline via workflow_dispatch + PR gate | Человеко-инициируемый кросс-репо pipeline: content-repo → publication-repo через параметризованный workflow_dispatch (guide_id, version) → генерация артефактов по шаблону → gh pr create в целевом репо. PR-гейт обеспечивает editorial review перед слиянием в публичное дерево. Применим для любого паттерна «источник контента → публичная витрина». | emerging |
| DP.M.105 | workflow_call orchestration: единый entry point с разделёнными concerns в CI/CD | — | active |
| DP.M.106 | Literature crosscheck при именовании Pack-сущностей | При создании новой роли/концепции/метода в Pack — обязательный прогон через 3-4 канонических литературных источника области, выбор имени closest-to-canon вместо собственного. Защищает от re-naming через 3-6 месяцев. | active |
| DP.M.107 | Role Rename Downstream Review | — | active |
| DP.M.108 | Specializes Vs Parallel Roles | — | active |

## Work Products

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.WP.001 | Отчёт экстракции | Структурированный отчёт экстракции знаний с классификациями, предложениями и валидацией | draft |
| DP.WP.002 | Ubiquitous Language | Единый язык домена: глоссарий терминов, прорастающий во все артефакты — код, UI, документацию, тикеты, планы | draft |
| DP.WP.003 | DayPlan | Ежедневный план работы: приоритеты, бюджеты, carry-over с предыдущего дня | draft |
| DP.WP.004 | WeekPlan | Еженедельный план: итоги прошлой недели, РП текущей, бюджеты, контент-план, сверка со стратегией | draft |
| DP.WP.005 | WeekReport | DEPRECATED: итоги недели теперь записываются в секцию «Итоги W{N-1}» внутри WeekPlan (DP.WP.004). Отдельный файл WeekReport больше не создаётся. | deprecated |
| DP.WP.006 | Fleeting Notes | Быстрые заметки пользователя: мысли, задачи, наблюдения — сырьё для Note Review и экстракции | draft |
| DP.WP.007 | Consistency Report | Отчёт проверки согласованности Pack-репо и downstream: расхождения, битые ссылки, дупликаты | draft |
| DP.WP.008 | Code Scan Report | Ежедневный отчёт по коммитам за 24ч: репо, авторы, ключевые изменения, TG-нотификация | draft |
| DP.WP.009 | Unsatisfied Questions Report | Еженедельный отчёт неудовлетворённых вопросов из feedback_triage DB: кластеры проблем, severity | draft |
| DP.WP.010 | CQRS Pack Projection | YAML-проекция Pack frontmatter для knowledge-mcp: read-optimized view Pack-сущностей | draft |
| DP.WP.011 | Triage Backlog | Приоритизированный backlog техдолга: баги, UX-проблемы, knowledge gaps — из Triage Session | draft |
| DP.WP.012 | Analytics Report | Аналитический отчёт бота: метрики использования, тренды, качество ответов | draft |
| DP.WP.013 | Publication Schedule | Расписание публикаций: посты со статусом ready → запланированные даты/время публикации в клубе | draft |
| DP.WP.014 | Validation Report | Отчёт валидации: проверка шаблона экзокортекса (S24) или Pack-сущности (S38) на соответствие стандарту | draft |
| DP.WP.015 | WP-Registry | Реестр всех рабочих продуктов (РП) стратегии: номер, название, статус — единое место для навигации по всей истории работы | draft |
| DP.WP.016 | Stage Dependency Map (Карта этапов с зависимостями) | Формат рабочего продукта Аналитика ограничений (DP.ROLE.054): план работы по устранению ограничения, представленный как dependency graph без дат и часов. Узлы = этапы (внутри узла — параллельные работы и РП), рёбра = жёсткая зависимость («следующий этап начинается только после завершения предыдущего»), external-рёбра = зависимости от работ в других РП / репо. | draft |

## Failure Modes

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.FM.001 | Информация как знание | Необработанная информация ошибочно принимается за формализованное знание без экстракции | draft |
| DP.FM.002 | Смешение слоёв | Смешение слоёв архитектуры платформы: код в Pack, знания в Downstream, UI в архитектуре | draft |
| DP.FM.003 | Контекстная слепота AI-агента | Ускорение генерации модели без ресурсов на добычу контекста = ускорение самообмана. AI-агент не может сам получить живой контекст из реальной жизни | draft |
| DP.FM.004 | Narrow Pregeneration Scope | — | draft |
| DP.FM.005 | Дрейф модель–реальность (Model-Reality Drift) | AI-агент без петли измерения деградирует в самосогласованный текст: внутренняя непротиворечивость растёт, но близость к реальности падает. Двойной дрейф: мир меняется + цели агента дрейфуют | draft |
| DP.FM.006 | Когнитивный долг как следствие агентного ИИ | Агентный ИИ производит код быстрее, чем разработчики успевают строить теорию системы. Техдолг — в коде, когнитивный долг — в головах. Программа — это теория в головах, код — лишь проекция | draft |
| DP.FM.007 | Дрейф представлений (View Drift) | View-файлы (README, CLAUDE.md, REGISTRY, посты) рассинхронизируются с model-файлами (Pack-сущности, код, файловая система). Причина: отсутствие автоматической валидации claims в view-файлах при изменении model. | draft |
| DP.FM.008 | Auto-Promote Without Confirmation | Автоматический промоут черновика без подтверждения пользователя — нарушение human-in-the-loop gate | active |
| DP.FM.009 | Protocol Hardcoded Script Path | Протокол ОРЗ хардкодит абсолютный путь к скрипту — ломается при любом перемещении скрипта. Симптом: exit 127 (no such file or directory). | resolved |
| DP.FM.010 | Agent Failure Patterns Catalog | Каталог повторяющихся паттернов провалов Claude-агента в рамках IWE. Корень дерева защит: паттерн → правило → детектор → Capture. Плоская нумерация P1-PN, постепенное развёртывание в отдельные DP.FM.XXX по мере обкатки | draft |
| DP.FM.011 | Not Capturing Patterns | Агент реагирует на провал записью нового правила в feedback_*.md, не обобщая его в паттерн. Правила множатся без роста compliance. Мета-корень дерева провалов: без защиты от P1 деградируют все остальные детекторы | draft |
| DP.FM.012 | Lexical Deduplication (Lossful Ontology Merge) | ИИ-агент при запросе 'убери дубли' выполняет лексическую нормализацию (совпадение слов/имён) вместо онтологической унификации (совпадение сущностей). Результат: семантически разные концепты сливаются в один, различения теряются без следа. | active |
| DP.FM.013 | Conservative Rewrite Failure (Смысловая контаминация при перефразировании) | При пересказе, переводе формата или summary ИИ-агент добавляет новые предположения и термины, которых не было в исходнике. Граница между 'перевыражением той же вещи' и 'переинтерпретацией с сочинительством' не контролируется. | active |
| DP.FM.014 | Legacy Port Jump (Прыжок в новый дизайн без проверки legacy) | При замене legacy-компонента (миграция из внешней системы, старой кодовой базы, LMS) агент прыгает сразу в проектирование нового дизайна, не выяснив как работает существующий механизм. Результат — перерасход часов в 3-5 раз или потеря рабочего решения. | active |
| DP.FM.015 | False-Positive Capture Detection (grep vs awk) | — | active |
| DP.FM.016 | Decay конфигурационных путей | — | active |
| DP.FM.017 | Asymmetric Env Cleanup (Асимметричная очистка env-переменных) | Smoke-test устанавливает несколько env-переменных с эфемерными путями (/tmp/iwe-smoke-*), но cleanup сбрасывает не все → ночные/non-interactive запуски падают с path-ошибками. | active |
| DP.FM.018 | Markdown Display-маркеры в data-полях (Markdown Markers in Data Fields) | Поля Markdown-таблиц содержат display-разметку (**bold**, ~~strike~~), корректную для рендеринга, но ломающую downstream text-processing (sed, awk, jq, commit messages). | active |
| DP.FM.019 | L3 Identity Leak (Утечка авторской идентичности в шаблон) | §9 (авторское) FMT-шаблона содержит конкретные имена/ID/пути пилота вместо {{PLACEHOLDER}} — при обновлении шаблона через update.sh эти данные распространяются на всех пользователей. | active |
| DP.FM.020 | Gateway SC без security disclosure для upstream credentials | SC для Gateway-компонента с upstream-proxy не содержит явного раздела «Безопасность» с MITM-disclosure. Потребитель не знает, что Gateway видит его OAuth-токены при proxying. Нарушение принципа informed consent в security архитектуре. | active |
| DP.FM.021 | Zero-slot blocks min aggregation | — | — |
| DP.FM.022 | systemd-minimal-path | — | active |
| DP.FM.023 | service-user-credentials-path | — | active |
| DP.FM.024 | git-pull-in-production — слияние build/release/run в агентах и launchd | — | active |
| DP.FM.025 | Монорепо с независимыми сервисами — нарушение 12-factor F1 | — | active |
| DP.FM.026 | .env в git history — утечка secrets + обязательные шаги ликвидации | — | active |
| DP.FM.027 | Railway Missing Auto-Deploy (Ручной деплой без git-интеграции) | Railway-проект развёртывается вручную (кнопкой), а не через git-webhook. Признак: отсутствие RAILWAY_GIT_* env-переменных и reason='deploy'/'redeploy' вместо 'github_push' в deployments API. Следствие: код в git не соответствует задеплоенному без явного ручного действия. | active |
| DP.FM.028 | Event Coverage Gap — новый модуль без аудита эмиссии событий | При добавлении нового workflow-модуля не проводится аудит event coverage: модуль доставляет пользовательские действия без эмиссии domain_event. Downstream системы (stage_evaluator, activity hub) видят пустой stream — активность пользователя не учитывается. | active |
| DP.FM.029 | Cross-Platform Path Leak (Утечка платформо-специфичных путей) | В конфигурации или коде кросс-платформенного инструмента прописан платформо-специфичный путь (macOS /Users/... slug, Windows C:\...). На целевой платформе (Linux/сервер) путь не существует, инструмент молча выдаёт WARN и продолжает работу — без явной ошибки. | active |
| DP.FM.030 | Compliance Matrix Narrative Drift (дрейф нарратива от ячеек матрицы) | При инкрементальном заполнении compliance-матрицы нарратив-секция обновляется реже ячеек таблицы. Числа в тексте расходятся с реальными counts — drift обнаруживается только при независимом review. | active |
| DP.FM.031 | Hardcoded Os Path | — | active |
| DP.FM.032 | Repair-Pass Stale-Hash Blind Spot (Слепое пятно устаревшего файла при repair-pass) | Repair-pass проверяет только отсутствие файла (! -f), но не его актуальность (hash vs source). Если файл существует, но содержимое расходится с FMT-source, он остаётся без обновления. Silent stale-регрессия. | draft |
| DP.FM.033 | Bash arithmetic increment под set -e (Bash Arithmetic Increment Under set -e) | Конструкция `((var++))` возвращает exit code 1 при var=0 (post-increment) — под `set -e` вызывает тихий abort скрипта без сообщения об ошибке. | active |
| DP.FM.034 | Pack-шифры в теле текста руководства | — | active |
| DP.FM.035 | CI live-config patch — iteration debt от хрупких примитивов | — | active |
| DP.FM.036 | WakaTime Measurement Scope Bias (Систематическое завышение через трекер без доменного scope) | Системный трекер активности (WakaTime, IDE session, GitHub commits) измеряет все репо без фильтра по домену. При использовании как прокси «инвестиций в X» — систематическое завышение в 3-5×. | draft |
| DP.FM.037 | Парсинг состояния по заголовку шаблона vs значению из frontmatter (Markdown Header Presence vs Frontmatter Value State Detection) | Детектор состояния использует `grep` по заголовку секции (`### 🔴 Critical`), который присутствует в шаблоне всегда — false-positive при пустой секции. Состояние должно парситься из значения в YAML frontmatter, а не из наличия заголовка. | active |
| DP.FM.038 | Silent-Pass Validator on Missing Input (Валидатор зеленеет на отсутствующем входе) | Валидатор на отсутствующем или пустом входе возвращает exit 0 (нечего нарушать), создавая false-green в CI/pre-commit. Опечатка в пути или несостоявшийся checkout → нулевая проверка → ложно-положительный сигнал. | draft |
| DP.FM.039 | Zero-Data Phase Cold Start (Нулевые значения при запуске нового metric pipeline) | Новый metric pipeline после запуска видит ноль у всех пользователей — исторические данные ещё не накоплены в новом формате. Без human-fallback система выдаёт «нет активности» → неверные показатели с первого дня. | draft |
| DP.FM.040 | Silent-Null Parser on Unknown Syntax (Парсер молча возвращает null) | Парсер ad-hoc форматов возвращает '' / null на не распознанный синтаксис вместо exception. На пустых данных тесты зелёные; слепая зона активируется когда поле начинают заполнять — все записи проходят валидацию пустыми. | draft |
| DP.FM.041 | Dedup Slice False Positive | — | active |
| DP.FM.042 | Same Schema Neon Dbs | — | draft |
| DP.FM.043 | Case Enum Assumption | — | draft |
| DP.FM.044 | Retroactive Backfill Regime Mismatch | — | draft |
| DP.FM.046 | Render-queue timeout — отсутствующий deadline на вызов подзадачи | Задание зависает в очереди навсегда, потому что воркер ждёт ответа от подзадачи без явного timeout. Диагностика: open-sessions log. Признак: задание в статусе «выполняется» дольше expected_max. | active |
| DP.FM.047 | Third Party Pii Vendor Gate | — | draft |
| DP.FM.048 | Cf Bot Fight Mode Xhr Block | — | active |
| DP.FM.049 | Document-centric analysis yields false bottleneck | — | active |
| DP.FM.050 | Markdown Bold Regex Punctuation | — | active |
| DP.FM.051 | On Conflict Nullable Unique Incompleteness | — | draft |

## SoTA Annotations

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SOTA.001 | DDD Strategic (Khononov) | Стратегический DDD: Bounded Context, Context Map, Ubiquitous Language — метод добычи и инженерной реализации доменного ядра | active |
| DP.SOTA.002 | Context Engineering | Дисциплина курирования контекста ИИ-агента: Write/Select/Compress/Isolate — что попадает в окно, в каком формате, как обновляется | active |
| DP.SOTA.003 | Open API Specifications | Экосистема открытых спецификаций интерфейсов: OpenAPI (sync), AsyncAPI (event-driven), CloudEvents (envelope) + Arazzo (workflows) | active |
| DP.SOTA.004 | GraphRAG + Knowledge Graphs | Комбинация vector search + knowledge graph traversal для multi-hop reasoning: 87% vs 23% accuracy по сравнению с базовым RAG | active |
| DP.SOTA.005 | AI-Native Org Design | Организационная архитектура для AI-first: плоские иерархии, agent orchestration, end-to-end accountability вместо функциональных силосов | active |
| DP.SOTA.006 | Agentic Development | Multi-agent orchestration: инженеры оркестрируют специализированных агентов, а не пишут код напрямую; requirement-to-deploy за часы | active |
| DP.SOTA.007 | AI-Accelerated Ontology Engineering | LLM ускоряют онтологическую инженерию в 10x: моделирование, расширение, population, alignment, entity disambiguation | active |
| DP.SOTA.008 | Real-Time Knowledge Capture | Capture during work, not after: знания фиксируются в момент обнаружения, а не ретроспективно — консенсус KM 2026 | active |
| DP.SOTA.009 | Knowledge-Based User Models (Persona / Memory / Projection) | Персональные/enterprise knowledge graphs и user models как трёхслойная архитектура: декларативная Персона (user-owned), наблюдаемая Память (platform-owned), runtime Проекция (ephemeral). Эволюция термина 'digital twin' в LLM-эру. | active |
| DP.SOTA.010 | DSL → DSLM Evolution | Бифуркация DSL: классические domain-specific languages стабильны, фронтир ушёл в Domain-Specific Language Models (DSLM) | active |
| DP.SOTA.011 | Coupling Model (Khononov 2024) | Многомерная модель связанности: knowledge coupling, distance coupling, volatility coupling — 4 уровня интеграции | active |
| DP.SOTA.012 | Multi-Representation Knowledge Architecture | Model/View эволюционировал в multi-representation: vector + graph + hierarchical index, отделённые от surface (бот, курс, API) | active |
| DP.SOTA.013 | World Models | Переход от LLM (модели знаний о мире) к World Models (модели мира): замыкание цикла действие-измерение-обновление, три линии исследований, архитектурные импликации для AI-агентов | active |
| DP.SOTA.014 | MCP как де-факто стандарт 2026 | Model Context Protocol — универсальный стандарт подключения AI-агентов к enterprise-инструментам. 97M+ скачиваний SDK, 75+ коннекторов | active |
| DP.SOTA.015 | AI/LLM System Observability (3+1 Framework) | SOTA-модель observability для AI/LLM: 3-сигнальная телеметрия (Traces/Metrics/Logs) + AI-специфичный слой Evaluations. «4-слойная AI observability» как именованный стандарт не существует. | draft |
| DP.SOTA.016 | Database-per-Service (паттерн изоляции данных) | Каждый сервис владеет собственной базой данных. Схема ≠ изоляция. FK между сервисами заменяются API-контрактами или событиями. | active |
| DP.SOTA.017 | Концептуальные графы — мировой опыт | Паттерны управления knowledge graphs: orphan-prevention, центральные узлы, многоязычность, editorial pipeline. Источники: OBO Foundry, Microsoft GraphRAG, Knowledge Space Theory (ALEKS), Wikidata. | active |
| DP.SOTA.018 | Управление терминологией в многоязычных онтологических системах | Паттерны управления терминологией из ISO 704, SKOS, DDD UL и реальной практики крупных проектов — применимость к IWE | active |
| DP.SOTA.019 | Граф как runtime-инструмент агента + наблюдаемость | Паттерны использования concept-графа агентом в runtime (Graph-RAG 2024-2026) + observability KG в продакшене + feedback loop от usage к эволюции графа. Дополняет DP.SOTA.004 (общая технология) и DP.SOTA.017 (структурная гигиена). | active |
| DP.SOTA.020 | Quantum-Like Modeling Lens (FPF C.26*, 2026) | Математическая линза для систем с probe-coupled state change, order effects, incompatibility, false composition. QL-lite режим как дополнение к классическому набору, не замена. | active |
| DP.SOTA.021 | State-Based Management vs Task-List Management | Управление через отслеживание состояний значимых объектов даёт измеримый эффект в системах с быстрой динамикой; task-list режим работает только при медленной реальности. Тест темпо-адекватности — критерий выбора. | active |
| DP.SOTA.022 | Agent Trace, Replay & Multi-Path Execution | SOTA-обзор архитектурных паттернов для журнала решений LLM-агентов, повтора (replay) и параллельного многопутевого исполнения (multi-path / best-of-N). Дополняет DP.SOTA.015 (telemetry layer) — этот документ про rationale layer. | draft |
| DP.SOTA.023 | Инженерная семиотика — мировой опыт | SOTA по инженерной семиотике для Pack-архитектуры IWE: триада Пирса, ISO 15926 (Kinds/Owner Roles), DDD Ubiquitous Language, OWL/SKOS. Что берём, что отвергаем, матрица применимости. | active |
| DP.SOTA.024 | BORO Methodology — Fundamental Particles & Fruitful Patterns | SOTA-аннотация методологии BORO (Business Objects Re-Engineering for Re-Use, Partridge): фундаментальные онтологические частицы и гипотеза о межпроектной fruitfulness паттернов. trust: hypothesis. | active |
| DP.SOTA.025 | BORO — 4D Ontology & Naming Pattern | SOTA-аннотация вклада BORO в 4D-онтологию (ISO 15926 family) и универсального naming-паттерна как framework-level reusable структуры. trust: hypothesis. | active |
| DP.SOTA.026 | Unified pipeline + content-hash skip — альтернатива дубль-pipeline для одного state | Анти-паттерн: два кода (delta + full-rebuild) для одного derived state → drift risk. Паттерн: единая функция reindexFor(files[]) idempotent + content_hash skip → полный rebuild почти-нулевой стоимости; webhook / heartbeat-cron / manual вызывают одну точку. | draft |

## Maps

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.MAP.001 | Pack Navigation Map | — | — |
| DP.MAP.002 | IWE Service Catalog | Кросс-системный каталог всех сервисов IWE: сервис → роль → вход → выход → потребитель → исполнитель → триггер | draft |

## Domain-Specific Entities

### AISYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.AISYS.008 | ДЗ-чекер | ИИ-система автоматической проверки домашних заданий учеников по нормативам из руководств | active |
| DP.AISYS.013 | Знание-Экстрактор | Prompt-based ИИ-система экстракции знаний из сессий в Pack-совместимые сущности и DS docs/ через двойной routing | draft |
| DP.AISYS.014 | AIST Bot | Telegram-бот экосистемы: тонкий клиент с сервисным реестром, ИИ-консультантом, биллингом и связью с цифровым двойником | draft |
| DP.AISYS.015 | Анализатор проговаривания и письма | ИИ-система анализа текста/речи на предмет использования понятий, выявления мемов и обновления модели мастерства ученика | active |

### ARCH

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ARCH.001 | Архитектура платформы | 3-слойная архитектура ИТ-платформы с 7 характеристиками (ЭМОГССБ) и 25 принципами: эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность, безопасность | active |
| DP.ARCH.002 | Тиры платформы | 4-осевая модель тиров: T0–T4 (учащийся) + TM1–TM3 (наставник) + TA1–TA4 (администратор) + TD1 (разработчик). Каждый тир — конфигурация среды по 5 измерениям. Оси ортогональны: один человек = T + TM? + TA? + TD? | draft |
| DP.ARCH.003 | Архитектура Digital Twin — единая точка расчёта и чтения | 8 принципов разделения Calculator / Reader. Единственный калькулятор — R28 Profiler. Интерфейсы — stateless витрины. Каждая цифра трассируется к IND-коду метамодели. | active |
| DP.ARCH.004 | Архитектура данных Neon (Database-per-BoundedContext) | 12 баз данных Neon по принципу database-per-BoundedContext. Сводная таблица, карта, ERD по каждой БД, связи, потоки, реестр физ.объектов с маркерами О/С/Р/К, revenue-sharing механика (контракты/сплиты/выплаты), points-ledger (event-sourcing) + эмиссионный отчёт, верификация по чеклистам SPF.SPEC.005, замечаниям Андрея Д1-Д12 и категориям WP-257. | active |
| DP.ARCH.005 | Персона (декларативная модель созидателя) | Персона — декларативный слой модели пользователя. Писатель = пользователь (или агент по его поручению с acceptance), владелец = Git-репо пользователя (PACK-personal, DS-my-strategy, captures). Платформа — read-only. Заменяет часть монолита ЦД (DP.ARCH.003). | approved |
| DP.ARCH.006 | Память (Observed события + Derived агрегаты) | Память — операционный слой модели пользователя. Писатель = платформа runtime, владелец = Neon. Два под-слоя: Observed (append-only события) + Derived (вычисляемые агрегаты, бывший узкий ЦД). Event Sourcing + CQRS. BKT, HLR, engagement, misconceptions, qualifications. Замещает основную часть монолита DP.ARCH.003. | approved |
| DP.ARCH.007 | Проекция (runtime-компиляция под потребителя) | Проекция — эфемерный runtime-слой: агент на лету собирает из Памяти и Персоны ответ под одного потребителя (LLM-промпт, пользовательский view, nudge). Writer = агент в runtime. Owner = нет (не хранится дольше одного вызова). Заменяет часть монолита ЦД (DP.ARCH.003) — §7 Views + §17 Nudge Engine. | approved |
| DP.ARCH.008 | Enforcement требует наблюдателя вне субъекта | Архитектурный принцип реализации правил агента: правило, которое проверяется самим агентом по памяти, имеет нулевую силу. Наблюдатель должен находиться ВНЕ субъекта, действия которого он контролирует. Шкала сил: memory (0) → hook (средняя) → deterministic generation (максимальная). | approved |
| DP.ARCH.009-decisions | Decisions | — | active |

### ASSIST

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ASSIST.001 | ИИ-ассистенты (superseded) | Объединены с DP.ROLE.001 — различие агент/ассистент сохранено как характеристика | superseded |

### CONCEPT

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.CONCEPT.001 | Концепция платформы | Концепция ИТ-платформы экосистемы: цифровой двойник, ИИ-системы, интеграции, отчуждаемость | active |
| DP.CONCEPT.003 | Адаптивная персонализация | Принцип и механизм платформы: адаптируется под человека через три слоя — персонализацию, индивидуализацию и адаптивность | active |
| DP.CONCEPT.004 | Three Layers Ai Work | 3 слоя работы с ИИ: разовый запрос (нет контекста) → роль и инструкция (постоянный системный контекст) → накопленная среда (история решений, документы, проекты). Переход между слоями определяется объёмом переданного контекста, а не моделью или промпт-техникой | draft |

### ECON

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ECON.001 | Points Engine — движок начисления баллов | Доменная модель системы баллов: сущности, инварианты, формула, потоки. Source-of-truth для Points Engine (WP-121, WP-311). Текущая реализация: база rewards (Neon). | draft |

### EXOCORTEX

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.EXOCORTEX.001 | Модульный экзокортекс | 3-слойная архитектура инструкций ИИ-агентов: CLAUDE.md + Memory + repo-CLAUDE.md | draft |

### IWE

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.IWE.001 | Intellectual Work Environment (IWE) | IWE — персональная интегрированная среда для интеллектуальной работы. Описывается через 5 архитектурных видов (ISO 42010): системы (U.System), описания (U.Description), роли (U.RoleAssignment), методы (U.MethodDescription), рабочие продукты (U.Work). Триада A.7: Роль → Метод → Рабочий продукт. Позиционирование: почему именно IWE, а не агенты/экзокортекс/FPF по отдельности. | draft |
| DP.IWE.002 | IWE Template & Setup | Практическое знание о шаблоне IWE: установка, ежедневная работа (ОРЗ), кастомизация (strategy_day, AUTHOR-ONLY зоны, конфиги), роли, обновление, FAQ. Source-of-truth для бота и MCP. | draft |
| DP.IWE.003 | Gateway-архитектура IWE | — | active |
| DP.IWE.004 | Интерфейсы IWE — различения клиентов | — | active |
| DP.IWE.005 | Local MCP Gateway (in-process multi-agent layer) | — | draft |
| DP.IWE.006 | Personal Guide Channels | — | draft |
| DP.IWE.007 | 5 природ IWE (Five Natures of IWE) | Пять UX-природ IWE — чем IWE является для пилота: Мастерская (среда ежедневной работы), Железный человек (костюм-расширитель), Аватар (узел сети сопроизводителей), Тамагочи (выращиваемый питомец, требующий ухода), Наставник (ведёт по траектории развития). Дополняет 5 архитектурных видов DP.IWE.001 (ISO 42010) — другая онтологическая ось: природы про «чем IWE является для пилота», виды архитектуры про «как описывать IWE». Порядок природ отражает приоритет: работа → жизнь → обучение. Источники: пост club-126 (4 мая 2026), посты TG 675 + 679 + 143, уточнение пилота 2026-05-18 (+5-я природа), уточнение пилота 2026-05-20 (Со-творец → Тамагочи, reorder). | draft |

### KR

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.KR.001 | Маршрутизация знаний IWE | Полная карта маршрутизации: какой тип контента куда записывать — от ZP до memory/, от Pack до 0.9.Inbox. Единый source-of-truth для агента и пользователя | draft |

### METHOD

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.METHOD.010 | Kinds + Owner Roles | Формальная процедура старта онтологической работы: сначала определить Kinds (типы сущностей) и Owner Roles (кто source-of-truth), только потом выравнивать лексику. Предотвращает DP.FM.012. | active |
| DP.METHOD.020 | Траектория развития Созидателя | Как строить траекторию персонального развития через 5 ролей Созидателя, ступени и степень квалификации. Для Навигатора, Портного и системы персональных руководств. | active |
| DP.METHOD.030 | Метод перевода терминов IWE | Воспроизводимый алгоритм выбора name_en для Pack-понятия с RU-каноном и обратно. EN — pivot-язык для последующих переводов. | active |
| DP.METHOD.031 | Метод онтологического сопоставления Pack-понятий с FPF-корнями | Алгоритм назначения FPF-корня (U.*) для нового Pack-понятия. Предотвращает изолированные понятия и silent drop рёбер при индексации. | active |
| DP.METHOD.040 | Метод ER-моделирования | Правила построения концептуальных ER-диаграмм: сущности физ.мира, связи между ними, трансформация в физическую схему РСУБД. Применяется при проектировании новых БД и при ревизии существующих. | active |
| DP.METHOD.041 | Метод связывания доменных сущностей с физ.реализацией | Правило связывания доменных сущностей Pack (DP.D.*, DP.CONCEPT.*) с физ.реализацией (таблица БД в DP.ARCH.004 §10) и кодовой реализацией (DS-файлы/модули). Сохраняет OwnerIntegrity: один факт — одно место, обратная ссылка из Pack в реализацию есть, но источник правды — DP.ARCH.004. v2 (24 апр, WP-228 Ф30) расширен §4 ARCH-bump sync-процессом и §5 антипаттерном дублирования формулировок downstream. | active |
| DP.METHOD.042 | Сценарии использования concept-графа агентами в runtime | 4 сценария применения concept-графа агентами платформы IWE: Claude Code (я), автор Pack, ролевые агенты бота (Портной/Оценщик/Навигатор), учебная траектория. Каждый описан по шаблону IntegrationGate шаг 2: потребитель → триггер → запрос → использование → observable-сигнал. | draft |
| DP.METHOD.050 | Метод применения Quantum-Like Lens (QL-lite) | Дисциплина применения quantum-like линзы FPF C.26* в проектировании метрик, диагностики, наблюдаемости и архитектурных решений. Активируется только при остаточной запутанности после классического набора. Включает 5 предохранителей и явный критерий выхода. | active |

### NAV

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.NAV.001 | Навигация знаний | 4-уровневая навигация знаний между репозиториями: FPF → SPF → Pack → Downstream | draft |

### ONT

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ONT.001 | Онтология платформы | Единая онтология домена «Цифровая платформа развития интеллекта»: 5 первичных родов сущностей (Созидатель, ИТ-система, Действие, Организация, Артефакт), маршрутизация описаний (type-level → Pack, instance-level → Neon/DS/R2/Legacy), виды сущностей по SPF.SPEC.001, глоссарий, отношения, иерархия типов, кросс-Pack связи, реестр различений, аббревиатуры. | active |

### ORG

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ORG.001 | Организация (род сущности) | Организация — коллективный субъект платформы: юр.лицо или сообщество со службами, сотрудниками, процессами. Первичный род наряду с Созидателем, ИТ-системой, Действием, Артефактом. Подтипы: МИМ, Aisystant, ШСМ. Целевая физ.реализация — схема platform-core #1 Neon (organizations/departments/employments) через ArchGate при первом FK. | draft |

### ROADMAP

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ROADMAP.001 | План миграции Neon 9 → 12 БД | Фазовый план перехода Neon с 9 БД (v1 14 апр) на 12 БД (согласно DP.ARCH.004 §1 v2.3). P0 подготовка, P1 низкорисковые переименования, P2 роспуск activity-hub, P2b dt-collect миграция на event-gateway, P3 расщепление platform, P4 knowledge split + aist-bot, P5 новые БД (#10/#11/#12), P6 decommissioning, P7 verification ongoing. Gating-критерии, rollback playbook, координация с child-WP, матрица рисков. | draft |
| DP.ROADMAP.002 | Neon MVP-greenfield (infra-first, старт 24 апр) | Параллельный к основному Roadmap план: MVP-greenfield на 12 целевых БД (DP.ARCH.004 v2.4), infra-first. Cut-over W18 executed 26-27 апр. Ф9.1-Ф9.4 internal gates PASS, Ф9.5 core-team prep активен, Ф9.6-Ф9.8 запланированы. Нумерация Ф9.X выровнена с context-файлом WP-253. | in_progress |

### ROLE

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ROLE.001 | ИИ-системы | Реестр и классификация ИИ-систем платформы: роли (Стратег, Экстрактор, Проводник и др.) и исполнители (Claude, бот, скрипты) | active |
| DP.ROLE.012 | 00 Role Passport | Роль Стратег (R1) преобразует намерения в структурированные планы рабочих продуктов на месяц, неделю и день с отслеживанием выполнения | draft |
| DP.ROLE.012.SC.01 | 01 Strategy Session | Еженедельная сессия стратегирования (strategy_day 7:00): ревью НЭП, анализ прошлой недели, сдвиг фокуса месяца, формирование плана на неделю | active |
| DP.ROLE.012.SC.02 | — План дня | Ежедневное планирование (7:00): апдейт вчера по коммитам, контекст недели и план дня с рекомендацией старта | draft |
| DP.ROLE.012.SC.03 | 03 Week Review | Итоговое ревью недели (вс 22:00): агрегация дневных планов, анализ коммитов, расчёт статистики и публикация в клуб | draft |
| DP.ROLE.012.SC.04 | 04 Month Report | Итоговый отчёт за месяц: агрегация недельных данных, проверка выполнения приоритетов, анализ трендов и достижений | draft |
| DP.ROLE.012.SC.05 | 01 Evening Review | Вечерний итог дня по запросу: сопоставление коммитов со статусами РП, выявление незапланированного, carry-over на завтра | draft |
| DP.ROLE.012.SC.06 | 02 Check Plan | Сверка задачи с планом по запросу: классификация на in-plan / aligned / unplanned / urgent с рекомендациями действия | draft |
| DP.ROLE.012.SC.07 | 03 Update Priorities | Изменение приоритетов на уровне дня/недели/месяца: определение типа изменения, каскадные эффекты, diff и коммит | draft |
| DP.ROLE.012.SC.08 | 04 Add Workproduct | Добавление нового РП в план: сбор атрибутов, проверка бюджета, определение уровня размещения и коммит в план | draft |
| DP.ROLE.012.SCENARIOS | 00 Scenarios Index | Индекс и навигация по 8 сценариям Стратега: 4 по расписанию и 4 по запросу, с временной сеткой и потоком данных | draft |
| DP.ROLE.022 | Оркестратор (Orchestrator) | Координатор цикла персонального развития: решает ЧТО и КОГДА запускать, делегирует исполнение специализированным Контролёрам и операционным ролям. На уровне суперсистемы координирует Контролёров (DP.ROLE.046 и его специализации); ниже — взаимодействует с Портным, Навигатором, Диагностом, Аттестатором, Проводником. | draft |
| DP.ROLE.031 | Терминолог | Роль Терминолог отвечает за качество терминологии Pack: выбор переводов, онтологическое сопоставление с FPF, разрешение конфликтов имён. | draft |
| DP.ROLE.032 | Event Ingester | Роль единого приёмника доменных событий обучения от всех источников — гарантирует идемпотентность, валидацию и защиту от PII на входе в learning.domain_event | draft |
| DP.ROLE.033 | Редактор контента | Роль, читающая черновики автора и выдающая рекомендацию топ-3 в Day Open на основе актуальности и готовности. | draft |
| DP.ROLE.034 | Rewards Projector | Роль проектора баллов: читает learning.domain_event, применяет reference.reward_rules через compute_effective_amount, пишет в rewards.point_balances идемпотентно через cursor | draft |
| DP.ROLE.035 | Platform Observer | Роль наблюдателя за здоровьем платформы — оркеструет Better Stack (external observability owner), AIST Bot (TG-алерты команде + автопостинг канал), Neon `health.internal_metrics` (узкая projection для JOIN с business). | draft |
| DP.ROLE.036 | Коннектор клуба | Носитель потока данных systemsworld.club (Discourse) → Neon. Read-only ingest активности участников через webhook + polling backfill, с lazy-резолвом discourse_user_id ↔ ory_identity_id после ORY-SSO. | draft |
| DP.ROLE.037 | Регистратор РП | Координатор целостности: гарантирует, что статус любого РП одинаков во всех 5 хранилищах IWE. Не исполняет работу по РП — исполняет работу ПО МЕТАДАННЫМ РП. | active |
| DP.ROLE.038 | MCP Tool Consumer | Посредник между LLM-клиентом (бот) и платформенными MCP-серверами: загружает актуальный список tool через discovery (tools/list), кэширует с TTL, фильтрует по tier, передаёт в Claude API без hardcoded списков в коде. | draft |
| DP.ROLE.039 | Peer Agent (равноправный peer-агент в multi-agent сессии) | Peer-агент в VS Code multi-agent сессии: подключается к Local Gateway, заявляет focus в peer-status, acquire lock перед write, sync через git sequential commits, escalates архитектурные разногласия к пилоту (не решает unilateral). Конкретные инстансы: Claude Code, Kimikode, Aider и т.п. | draft |
| DP.ROLE.040 | OAuth Orchestrator (единая точка OAuth-flows для всех каналов IWE) | Сервис-роль: принимает OAuth setup/callback запросы от web/vscode/bot каналов, разрешает identity (Ory > telegram > github), управляет state-token lifecycle, координирует token exchange с провайдерами (GitHub App, Linear, Twin, Google Cal, WakaTime, Ory), хранит токены encrypted-at-rest в Neon. Не зависит от bot process. | draft |
| DP.ROLE.041 | Аттестатор | Роль автоматического вычислителя ступени Ученика: читает события из Activity Hub, считает 7 bh-характеристик (bh.sys/inv/met/awr/agn/scl/stb) по двум осям (Мастерство × Мировоззрение), сравнивает с нормативной матрицей и записывает bh-сигнал в learning.stage_transitions. Итоговую ступень фиксирует двойной gate: bh-сигнал Аттестатора + cp-подтверждение Диагноста (MIM.R.009). Болид-онтология: Аттестатор измеряет Пилота, не всего Созидателя. | draft |
| DP.ROLE.042 | Диагност (R28) | Роль диалоговой и фоновой диагностики ученика: проводит диалог ≤5 вопросов (три фазы), вычисляет cp-профиль (ступень + bottleneck + recommended_stream + skip_to_stage), сохраняет в learning.cp_assessments. В фоновом режиме — silent-monitoring сигналов инвалидации и подсказки активным ролям (Навигатор / Портной / Аттестатор). Реализует двойной gate FORM.089 §5.1 с Аттестатором. | draft |
| DP.ROLE.043 | Лаборант | Роль симулятора траектории Созидателя: принимает профиль + паттерн поведения, запускает сценарий (Scenario.run() → DataFrame), возвращает траекторию характеристик и ступени во времени — в pilot-mode без технических кодов. | draft |
| DP.ROLE.044 | Notification Dispatcher | Транспортный слой исходящих уведомлений платформы: принимает запросы от любых потребителей (пользователь, агент, воркер), ставит в очередь, доставляет в Telegram exactly-once, подтверждает статус. | draft |
| DP.ROLE.045 | Agent Task Dispatcher | Координатор очереди агентных задач IWE: читает inbox/agent/tasks/, запускает через подходящий канал (CCR / systemd / local), фиксирует lifecycle и audit-trail. | draft |
| DP.ROLE.046 | Контролёр развития (Development Controller) | Ежедневный фоновый сканер: обходит опт-инов, сравнивает фактический профиль с маркером ожидаемого профиля по выбранной оси контроля (по умолчанию — ступени Ученика, FORM.089 §6.3; расширяемо на степени квалификации, стиль, домены). При обнаружении gap'a выдаёт точечное задание адресату по типу разрыва. Не оценивает, не назначает, не учит — только инициирует следующее действие. | draft |
| DP.ROLE.047 | Trace Recorder (Архивариус решений) | Записывает рассуждения LLM-агента (гипотезы, выбор, обоснование) в append-only журнал. Single source of truth для retrieval, replay, pattern mining. Не блокирует hot path. | draft |
| DP.ROLE.048 | Replay Engine (Машина повторов) | Восстанавливает состояние агента на момент T из trace + событий, создаёт fork-сессию. Детерминированное воспроизведение через checkpoint + reseed. Read-only по исходному trace. | draft |
| DP.ROLE.049 | Path Coordinator (Координатор путей) | Разворачивает N кандидатов параллельно на open-loop задачах с разными моделями/seed, координирует селектор, обеспечивает budget guard и сохранение всех путей в trace для последующего анализа. | draft |
| DP.ROLE.050 | Pattern Miner (Старатель паттернов) | Кластеризует trace'ы за период по (trace_features, outcome_features) join, формирует кандидатов AR.NNN с примерами, помечает status: pending-review. Никогда не создаёт правила автоматически. | draft |
| DP.ROLE.051 | Points Redeemer (Burn-эмиттер баллов) | Роль burn-эмиттера: при чекауте резервирует баллы в rewards.redeemed_events; при webhook'е оплаты подтверждает или откатывает резерв; эмитирует event 'points_redeemed' для projection-worker. Не writer point_balances. | draft |
| DP.ROLE.052 | Когнитивный прокси-аналитик | Извлекает косвенные характеристики (cp.wld, cp.agt, bh.awr) из текстового содержания пилота. Результаты используются ТОЛЬКО для рекомендаций (Портной, Диагност) — не для расчёта stage/certificate. Пишет ТОЛЬКО в cognitive-схему через scope guard. | draft |
| DP.ROLE.053 | R29 Артефактор | — | active |
| DP.ROLE.054 | R30 Аналитик ограничений | Носитель методики TOC (Goldratt Five Focusing Steps + Tendon TameFlow Replenishment Cycle + Dettmer Thinking Processes). Идентифицирует систему-конвейер, сканирует функциональные обещания (SC-first), находит ограничение, выбирает TOC-инструмент и выдаёт план работы как карту этапов с зависимостями (без дат/часов). Универсален: применим к учебному конвейеру пилота, конвейеру работ (РП/эпик/проект/репо), когортному конвейеру. | draft |
| DP.ROLE.055 | Агент поддержки IWE | Носитель ответа на пилотские обращения через Chatwoot CE: маршрутизирует тикеты по теме (баг → разработчик, баллы → диспетчер, руководство → методист), отвечает в Chatwoot, эскалирует в Linear, поддерживает FAQ и Saved Replies. Граница: НЕ диагностирует архитектурные баги (это R6 Кодировщик), НЕ принимает продуктовые решения по фичреквестам (это Стратег R1 + пилот). | draft |

### RUNBOOK

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.RUNBOOK.001 | Runbook: Aist Bot Errors | Операционный runbook. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |

### SC

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SC.001 | Планирование дня | Пользователь получает ясный план работы на день к началу рабочего дня | draft |
| DP.SC.002 | Планирование и ревью недели | Пользователь получает план недели на основе стратегии и итоги прошедшей недели | draft |
| DP.SC.003 | Обучение и развитие | Пользователь получает персонализированное развитие: вопросы, проверку ДЗ, ленту знаний, марафоны | draft |
| DP.SC.004 | Фиксация и экстракция знаний | Знания фиксируются в момент обнаружения и превращаются в формализованные Pack-сущности | draft |
| DP.SC.005 | Публикация контента | Автор пишет лонгрид для клуба (source-of-truth), согласовывает, адаптирует под каналы, публикует автоматически или вручную | draft |
| DP.SC.006 | Автоматическое обслуживание | Платформа автоматически синхронизирует данные, проверяет целостность и поддерживает инфраструктуру без участия пользователя | draft |
| DP.SC.007 | Триаж и техдолг | Негативная обратная связь автоматически классифицируется, а техдолг приоритизируется в сессиях триажа | draft |
| DP.SC.008 | Самовосстановление | Платформа автоматически обнаруживает и устраняет сбои — от зависших пользователей до критических ошибок | draft |
| DP.SC.009 | Аналитика и метрики | Пользователь получает агрегированные метрики по качеству ответов, активности и затратам времени | draft |
| DP.SC.010 | Рабочий ритм (ОРЗ) | Рабочий день и каждая сессия структурированы по циклу Открытие → Работа → Закрытие — ничего не забыто, всё зафиксировано | draft |
| DP.SC.011 | Стратегирование | Неудовлетворённости превращаются в приоритеты, а приоритеты — в утверждённый план недели | draft |
| DP.SC.012 | Онбординг | Новый пользователь настраивает IWE и понимает что делать — от первого запуска до первого рабочего дня | draft |
| DP.SC.013 | Рабочая сессия с Claude Code | Пользователь выполняет задачу с ИИ-ассистентом — от WP Gate до Close с фиксацией знаний | draft |
| DP.SC.014 | Формализация знаний (Pack) | Доменное знание формализуется в Pack-структуру — методы, различения, failure modes, SOTA, рабочие продукты | draft |
| DP.SC.015 | Развитие системы (DS) | Новая функциональность спроектирована и реализована — от UC Gate до работающего сервиса с PROCESSES.md | draft |
| DP.SC.016 | Коллективное управление рабочими продуктами | Команда с индивидуальными IWE работает над общими и назначенными РП — каждый видит свои задания, общую картину и прогресс коллег | draft |
| DP.SC.017 | Адаптивное задание на день | Платформа формирует персональный план дня для участника потока — с учётом тира, прогресса вчера, ступени квалификации и целей программы — и трекает выполнение | draft |
| DP.SC.018 | Переход T3 → T4 (присоединение к git) | Участник дорос до самостоятельного управления своим IWE — платформа помогает перейти от получателя заданий к их автору | draft |
| DP.SC.019 | Автономная работа IWE (Cloud Runtime) | IWE работает 24/7 в облаке: ночная автоматика, мультиустройственный доступ, управление через Telegram | draft |
| DP.SC.020 | Персональная программа развития | Платформа ведёт пользователя через программу «Личное развитие» — от ступени Случайный до Проактивный — через цикл диагностика → сборка занятия → доставка → оценка → фиксация прогресса. Четыре агентные роли (Диагност, Оркестратор, Портной, Навигатор) работают совместно, адаптируя содержание, темп и глубину под конкретного пользователя.
 | draft |
| DP.SC.021 | Mcp Knowledge Access | — | draft |
| DP.SC.022 | Personal Knowledge Indexing | — | draft |
| DP.SC.023 | Mcp Extensibility | — | draft |
| DP.SC.024 | Iwe Maintenance | — | draft |
| DP.SC.025 | Capture Bus | — | draft |
| DP.SC.026 | Мониторинг поведения агента | — | draft |
| DP.SC.027 | Repo Touch Gate | — | draft |
| DP.SC.028 | Семиотическое качество Pack | Pack-автор получает верифицированные сущности с корректной Kinds-структурой, защитой от лексической дедупликации и читаемым смыслом для агентов и людей | draft |
| DP.SC.029 | Терминологический процесс IWE | Автор понятия получает верифицированный перевод name_ru/name_en и сопоставление с FPF-корнем при вводе нового понятия в Pack | draft |
| DP.SC.031 | Personal Read Api | — | draft |
| DP.SC.032 | Personal Data View Audit | — | draft |
| DP.SC.033 | Целостность жизненного цикла РП | Стратег получает гарантию: статус любого РП одинаков во всех 5 хранилищах IWE в течение ≤5 минут после любого изменения. Нарушение = drift, обнаруживается автоматически. | active |
| DP.SC.034 | Local MCP Gateway для multi-agent VS Code | Peer-агент (Claude Code, Kimikode и т.п.) в одной VS Code сессии получает гарантию: tool-вызовы маршрутизируются через единую точку, конфликт записи в один файл предотвращается pessimistic-lock'ом, новый агент подключается без правки кода других агентов. | draft |
| DP.SC.035 | Peer-agent choreography (turn-based координация) | Пилот получает гарантию: два+ peer-агента (Claude Code + Kimikode и др.) в одной VS Code сессии работают параллельно над разными файлами без дублирования и race-condition. Координация — turn-based через lock API Local Gateway + git sequential commits для sync. | draft |
| DP.SC.036 | Knowledge Routing Gate — маршрутизация артефактов в IWE | Агент получает канонический путь размещения для любого нового артефакта до выполнения Write, используя каскад vocab → DP.KR.001 → repo CLAUDE.md | draft |
| DP.SC.037 | Agent Trace | — | draft |
| DP.SC.038 | Agent Replay | — | draft |
| DP.SC.039 | Multipath | — | draft |
| DP.SC.040 | Pattern Miner | — | draft |
| DP.SC.041 | Индикатор мультипликатора IWE в характеристике мастерства | Потребители (Аттестатор, Навигатор, Metabase) получают в digital_twins.data['3_derived']['3_2_mastery'] четыре числа: multiplier_auto, multiplier_manual, multiplier_drift, multiplier_7d_avg. Расхождение — сигнал, не ошибка. | active |
| DP.SC.042 | Извлечение знаний в Pack | Знания из сессий, обратной связи и документов преобразуются в Pack-сущности (правила, роли, методы, различения) и интегрируются в платформу | active |
| DP.SC.043 | Обновление экзокортекса | Пользователь получает обновления платформенных файлов шаблона — новые протоколы, скиллы, скрипты, исправления | draft |
| DP.SC.044 | Event Ingest (единый приёмник доменных событий) | Единая точка приёма доменных событий обучения от всех источников с идемпотентностью, валидацией и PII-фильтрацией | draft |
| DP.SC.045 | Анализ ограничения системы (TOC) | Потребитель (пилот / Стратег / Артефактор / Навигатор) получает на выходе пятифазного ВДВ-каскада три артефакта: System Card (классификация системы-конвейера), Constraint Brief (описание ограничения с trichotomy + class), Stage Dependency Map (план работы как dependency graph без дат и часов). SC-first: первой проверяется работоспособность функциональных обещаний, не структура pending-РП. | draft |
| DP.SC.101 | Вход и онбординг на платформе | Новый участник регистрируется, создаёт ЦД и получает персональный стартовый маршрут — от любопытства к первому действию | draft |
| DP.SC.102 | Непрерывное обучение | Участник изучает руководства, выполняет задания в рабочей тетради, получает обратную связь от наставника или ИИ | draft |
| DP.SC.103 | Работа над целевыми системами | Участник применяет методологию FPF к реальным проектам — от учёбы к созиданию | draft |
| DP.SC.104 | Адаптивная персонализация через Персону, Память и Проекцию | Платформа адаптируется под человека через три слоя пользовательской модели (Персона декларативная, Память наблюдаемая, Проекция runtime) и три механизма персонализации (персонализация/индивидуализация/адаптивность) | draft |
| DP.SC.105 | Экономика вклада — баллы и репутация | Участники получают баллы за подтверждённые действия, бонусы конвертируются в доступ к сервисам | draft |
| DP.SC.106 | Сообщество и культурная диффузия | Участники обсуждают, менторят, проверяют работы друг друга и несут культуру вовне | draft |
| DP.SC.107 | Мультиповерхностный доступ | Одна платформа, много интерфейсов — Web App, Telegram-бот, Claude Code CLI, Discord | draft |
| DP.SC.108 | Формирование команд | Участники формируют гибридные команды (люди + ИИ-агенты) для работы над целевыми системами | draft |
| DP.SC.109 | Масштабирование — Global Core + Local Edge | Платформа масштабируется через единое ядро методологии и локальные адаптации (язык, кейсы, compliance) | draft |
| DP.SC.110 | Управление потоками и наставничество | Администратор создаёт потоки, назначает наставников; наставник проверяет ДЗ и ведёт группу; сертификация автоматическая | draft |
| DP.SC.111 | Назначение на позицию | Администратор назначает позицию (бандл Role+Tier+Scope) одним действием — система раскладывает в три оси доступов | draft |
| DP.SC.112 | Подписка и оплата | От бесплатного старта к устойчивой подписке — тиры T1-T4, YooKassa/Stripe/TG Stars, баллы, revenue sharing | draft |
| DP.SC.113 | Авторство и Revenue Sharing | Автор создаёт руководство, публикует через рецензирование и получает долю дохода (50%) | draft |
| DP.SC.114 | CRM и работа с участниками | Воронка, удержание, отток, группы — управление на основе данных с проактивной работой с at-risk участниками. Directus UI + Telegram CRM-команды + Metabase дашборды | draft |
| DP.SC.115 | Маркетинг и привлечение | Привлечение участников через открытые руководства, рефералы, промо-коды и конверсионные триггеры в боте | draft |
| DP.SC.116 | Уведомления и nudges | Правильное сообщение в правильный момент — ЦД-инсайты, дедлайны, streaks, milestones, конверсия | draft |
| DP.SC.117 | Асинхронная проверка и обсуждение ДЗ | Ответы на ДЗ сохраняются в Память.Observed, проверяются пакетно, результаты персистентны и доступны для обсуждения | draft |
| DP.SC.118 | Ассистент упоминаний в каналах | Бот отслеживает упоминания пользователя в TG-каналах, генерирует черновик ответа через IWE и присылает в личку | draft |
| DP.SC.119 | Рабочее пространство из браузера | Пользователь создаёт и управляет IWE-пространствами из браузера — Pack, DS-репо, заметки — без git, без терминала, без VS Code | draft |
| DP.SC.120 | Приёмник платежей (Payment Receiver) | Webhook-приёмник: провайдеры (YooKassa, Stripe, Paybox) → verify → normalize → idempotent write → finance_payments (Neon) | draft |
| DP.SC.122 | Rewards Projection (точная проекция баллов по доменным событиям) | Точная идемпотентная проекция из learning.domain_event в rewards.point_balances по reference.reward_rules через LISTEN/NOTIFY | draft |
| DP.SC.123 | Platform Observability (internal — наблюдаемость инфраструктуры для команды) | Минимально достаточный набор сигналов о здоровье 12 БД и ~10 сервисов для команды: реактивные ответы, проактивные алерты, retro-queries. SaaS-first (Better Stack owner external observability) + узкая projection в Neon для JOIN с business-данными. | draft |
| DP.SC.124 | Lifework Pack Coaching | Созидатель получает поэтапную помощь Портного в составлении документа очередного уровня охвата пакета Lifework при условии, что документ предыдущего уровня работает ≥6 месяцев | draft |
| DP.SC.125 | Гостевой пропуск (реферальная виральность БР) | Подписчик БР приглашает друга → друг получает 14 дней бесплатного БР → при оплате друга и удержании 30 дней приглашающий получает 1 месяц БР | draft |
| DP.SC.126 | Подписка БР как массовый продукт | Участник получает персональную траекторию роста интеллекта на всю жизнь — не курс по навыкам, а среда с памятью о нём, которая адаптируется через методологию, платформу и адаптивную персонализацию | draft |
| DP.SC.127 | Редактор контента | Автор получает топ-3 черновика для работы и сигналы о готовых постах в Day Open | draft |
| DP.SC.128 | Ingest активности клуба (Discourse) | Платформа получает события активности участников клуба systemsworld.club для расчёта баллов и аналитики | draft |
| DP.SC.129 | Generic MCP Tool Discovery (бот → платформенные MCP) | Бот получает актуальный список tool из платформенных MCP при старте и периодически, без hardcoded списков в коде | draft |
| DP.SC.130 | OAuth Gateway (единая точка OAuth для всех каналов IWE) | Web/VS Code/Bot пилот получает доступ к внешним OAuth-провайдерам (GitHub App, Linear, Twin, Google Calendar, WakaTime, Ory) через единый endpoint с dual identity (telegram_user_id ИЛИ ory-session) | draft |
| DP.SC.131 | Автопроцесс резервного копирования данных IWE | — | — |
| DP.SC.132 | Диагностика ученика (Диагност) | Пилот (Ученик), Аттестатор, Портной или Навигатор получает cp-профиль (ступень, bottleneck, рекомендуемый поток) через диалог ≤5 вопросов или кэш-ответ, из любого из трёх интерфейсов (TG / браузер / VS Code) или в фоновом режиме | draft |
| DP.SC.133 | Симулятор траектории Созидателя | Пилот получает траекторию своих характеристик и ступени во времени при заданном паттерне поведения — в понятном тексте без технических кодов | draft |
| DP.SC.134 | Notification Dispatcher | Любой потребитель (пользователь, агент, воркер) получает доставку сообщения в Telegram — немедленно или по расписанию — с подтверждением и гарантией exactly-once | draft |
| DP.SC.135 | Agent Inbox — конвейер агентных задач IWE | Создатель IWE ставит задачу агенту в единое место и получает результат в декларированной точке не позднее чем через 1 час после due | draft |
| DP.SC.136 | Rewards Transparency (понимание пилотом источника своих баллов) | Пилот видит не просто число «у тебя X баллов», а понятную причинно-следственную цепочку: за что начислено, сколько по каждому правилу, какие правила игры действуют сейчас. | draft |
| DP.SC.137 | Rewards Analytics (аналитика начислений и прогноз скидок для команды) | Команда (R5 CRM/админ платформы) видит динамику начислений баллов, активные балансы по сегментам пилотов и ожидаемую нагрузку на платформу от конвертации баллов в скидки — без SQL, через Метабазу. | draft |
| DP.SC.138 | Rewards Rules Simulation Lab (симулятор «что если» для калибровки правил) | R2 Архитектор правил может за 5 минут получить ответ «что бы получили пилоты при таком наборе правил» — без деплоя, на исторических данных. Калибровка перед выкаткой. | draft |
| DP.SC.139 | Контролёр развития (Daily Marker Scan) | Опт-инный пилот ежедневно получает корректирующий нудж (TG, render-задача Портному, или сигнал Навигатору/Проводнику/Диагносту) по выбранному профилю контроля (по умолчанию — ступени Ученика и маркеры cp.iwe × cp.cre, FORM.089 §6.3). Профиль контроля расширяемо: степени квалификации, стиль, домены. | draft |
| DP.SC.140 | Club Action Catalog | — | active |
| DP.SC.141 | Зачёт баллов в оплату | Канал «Баллы» в Billing Module: участник применяет накопленные баллы как скидку к оплате сервиса (резерв-перед-оплатой, двухфазный коммит) | draft |
| DP.SC.142 | Текстовый анализ косвенных характеристик (cp.wld / cp.agt / bh.awr) | Портной и Диагност получают актуальные прокси cp.wld, cp.agt, bh.awr из текстового содержания пилота — ТОЛЬКО для рекомендаций. В расчёт stage/certificate не входят. | draft |
| DP.SC.143 | LMS Subscription Webhook (Bridge-2 контракт с LMS Aisystant) | Контракт endpoint'а на стороне LMS Aisystant для приёма подписок от нашего payment-receiver. Артефакт для передачи Диме. | draft-not-delivered |
| DP.SC.144 | User-Facing Platform Health (информирование пользователей о здоровье платформы) | Public status page (status.aisystant.ru) с composite uptime «по девяткам» (формат 99.847%), real-time информирование пользователей об инцидентах через email/RSS subscriptions + TG-канал @aisystant_status. Реализуется через Better Stack SaaS. | draft |
| DP.SC.145 | Llm Router | — | active |
| DP.SC.146 | Secret Drift Detector | — | active |
| DP.SC.147 | Агрегирующий пайплайн cognitive brief | Навигатор (MIM.R.007) перед ответом читает агрегированный brief из выходов Оркестратора, Портного, activity_log и Cognitive Proxy. Без text_analysis consent — только детерминированные поля. | draft |
| DP.SC.148 | Pack Graph Freshness | Pack-граф (concept_graph_nodes + edges) обновляется автоматически при push в Pack-репо и проверяется daily heartbeat + drift detector | draft |
| DP.SC.149 | Ретроспективный майнинг корпуса в PACK-rhetoric | Автор или агент получает пакет карточек иллюстраций из произвольного корпуса (клуб, руководства, книги) в формате RHE.FORM.001 при указании источника и фильтра тропа | active |
| DP.SC.150 | Поддержка пользователей IWE через @aist_me_bot + Chatwoot | Пилот через команду /support в @aist_me_bot открывает тикет в Chatwoot CE; служба поддержки получает структурированный контекст (telegram_id, ory_uuid, последние события), отвечает в Chatwoot — ответ доставляется в TG-чат пилота с префиксом 🛟; SLA ≤24ч на первый ответ | draft |
| DP.SC.151 | Контролёр развития (профиль Onboarding Tick) | Опт-инный пилот R2 получает поведенческий нудж (TG или render-задача Портному) по очереди из 11 онбординговых сообщений (WP-343). Сообщение приходит не по расписанию, а по реальному поведению пилота. Не более 1 нуджа в сутки. Следующее сообщение доставляется в течение 8h после срабатывания триггера. | draft |

### SYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SYS.001 | Детерминированные системы | Реестр детерминированных подсистем. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |

## Warnings

- Missing `summary`: DP.D.053 (DP.D.053-problem-task-workflow.md)
- Missing `summary`: DP.D.054 (DP.D.054-dashboard-audience-projections.md)
- Missing `summary`: DP.D.055 (DP.D.055-domain-vs-topic-test.md)
- Missing `summary`: DP.D.056 (DP.D.056-iwe-layer-portability.md)
- Missing `summary`: DP.D.057 (DP.D.057-routing-decision-vs-map-update.md)
- Missing `summary`: DP.D.058 (DP.D.058-service-clause-vs-carrier.md)
- Missing `summary`: DP.D.059 (DP.D.059-three-classes-credentials-storage.md)
- Missing `summary`: DP.D.060 (DP.D.060-entity-db-vs-special-db.md)
- Missing `summary`: DP.D.061 (DP.D.061-neon-db-count-layers.md)
- Missing `summary`: DP.D.062 (DP.D.062-sc-consumer-is-role-not-channel.md)
- Missing `summary`: DP.D.063 (DP.D.063-platform-vs-consumer-notifications.md)
- Missing `summary`: DP.D.064 (DP.D.064-same-vs-different-promise-wp-branch.md)
- Missing `summary`: DP.D.065 (DP.D.065-orthogonal-distinctions.md)
- Missing `summary`: DP.D.066 (DP.D.066-blueprint-vs-build.md)
- Missing `summary`: DP.ARCH.009-decisions (DP.ARCH.009-decisions.md)
- Missing `summary`: DP.D.067 (DP.D.067-card-vs-append-only-event.md)
- Missing `summary`: DP.D.068 (DP.D.068-audit-discovered-owner.md)
- Missing `summary`: DP.D.069 (DP.D.069-doc-wp-vs-impl-wp.md)
- Missing `summary`: DP.D.070 (DP.D.070-artifact-vs-artifact-mode.md)
- Missing `summary`: DP.D.071 (DP.D.071-declared-vs-actual-bounded-context.md)
- Missing `summary`: DP.D.072 (DP.D.072-format-spec-vs-format-checklist.md)
- Missing `summary`: DP.D.073 (DP.D.073-storefront-vs-internal-platform.md)
- Missing `summary`: DP.D.077 (DP.D.077-interface-vs-learning-onboarding.md)
- Missing `summary`: DP.D.078 (DP.D.078-value-vs-technical-language.md)
- Missing `summary`: DP.IWE.003 (DP.IWE.003-gateway-architecture.md)
- Missing `summary`: DP.IWE.004 (DP.IWE.004-iwe-interfaces.md)
- Missing `summary`: DP.IWE.005 (DP.IWE.005-local-gateway.md)
- Missing `summary`: DP.IWE.006 (DP.IWE.006-personal-guide-channels.md)
- Missing `summary`: DP.ROLE.053 (DP.ROLE.053-artifactor.md)
- Missing `summary`: DP.M.012 (DP.M.012-machine-check-postcondition.md)
- Missing `summary`: DP.M.014 (DP.M.014-evaluator-worker.md)
- Missing `summary`: DP.M.015 (DP.M.015-four-layer-gamification-dependency.md)
- Missing `summary`: DP.M.016 (DP.M.016-pack-domain-maturity-diagnostics.md)
- Missing `summary`: DP.M.018 (DP.M.018-external-data-fallback-hierarchy.md)
- Missing `summary`: DP.M.021 (DP.M.021-github-app-platform-integration.md)
- Missing `summary`: DP.M.022 (DP.M.022-cache-safe-personal-dashboard.md)
- Missing `summary`: DP.M.024 (DP.M.024-legacy-temporal-fallback.md)
- Missing `summary`: DP.M.025 (DP.M.025-wave-rollout.md)
- Missing `summary`: DP.M.026 (DP.M.026-git-fork-push-pattern.md)
- Missing `summary`: DP.M.028 (DP.M.028-stateless-worker-cursor-pattern.md)
- Missing `summary`: DP.M.029 (DP.M.029-audit-critical-cross-verify.md)
- Missing `summary`: DP.M.036 (DP.M.036-peer-agent-onboarding.md)
- Missing `summary`: DP.M.037 (DP.M.037-personal-guide-lifecycle.md)
- Missing `summary`: DP.M.041 (DP.M.041-posttooluse-hook-derived-sync.md)
- Missing `summary`: DP.M.043 (DP.M.043-artifact-lifecycle-archive.md)
- Missing `summary`: DP.M.045 (DP.M.045-automation-sc-three-axes.md)
- Missing `summary`: DP.M.046 (DP.M.046-keyset-pagination.md)
- Missing `summary`: DP.M.047 (DP.M.047-backup-stress-test.md)
- Missing `summary`: DP.M.050 (DP.M.050-env-i-isolation.md)
- Missing `summary`: DP.M.051 (DP.M.051-spawned-wp-from-phase.md)
- Missing `summary`: DP.M.052 (DP.M.052-dt-write-api-browser-channel.md)
- Missing `summary`: DP.M.053 (DP.M.053-pack-sot-code-mirror.md)
- Missing `summary`: DP.M.054 (DP.M.054-targeted-backfill-via-queue.md)
- Missing `summary`: DP.M.055 (DP.M.055-config-sot-triplet.md)
- Missing `summary`: DP.M.057 (DP.M.057-ml-component-ab-evaluation.md)
- Missing `summary`: DP.M.060 (DP.M.060-atomic-vdv-step.md)
- Missing `summary`: DP.M.066 (DP.M.066-multi-round-verifier.md)
- Missing `summary`: DP.M.067 (DP.M.067-two-pass-review-subagent-self.md)
- Missing `summary`: DP.M.068 (DP.M.068-scope-creep-corrective-quad.md)
- Missing `summary`: DP.M.069 (DP.M.069-multi-scenario-service-clause.md)
- Missing `summary`: DP.M.070 (DP.M.070-two-phase-hypothesis-test.md)
- Missing `summary`: DP.M.071 (DP.M.071-pre-implementation-smoke.md)
- Missing `summary`: DP.M.072 (DP.M.072-split-transaction-late-webhook.md)
- Missing `summary`: DP.M.073 (DP.M.073-pause-before-fix-controllers.md)
- Missing `summary`: DP.M.074 (DP.M.074-provisional-payment-id.md)
- Missing `summary`: DP.M.075 (DP.M.075-no-op-heartbeat.md)
- Missing `summary`: DP.M.076 (DP.M.076-migration-flag-warn-fail.md)
- Missing `summary`: DP.M.077 (DP.M.077-common-prefix-compression.md)
- Missing `summary`: DP.M.078 (DP.M.078-architectural-rule-propagation.md)
- Missing `summary`: DP.M.080 (DP.M.080-composite-indicator-weighted-providers.md)
- Missing `summary`: DP.M.081 (DP.M.081-pii-gate-synthetic-bypass.md)
- Missing `summary`: DP.M.082 (DP.M.082-wp-scope-boundary-via-sc-interfaces.md)
- Missing `summary`: DP.M.083 (DP.M.083-batch-frontmatter-enum-validator.md)
- Missing `summary`: DP.M.084 (DP.M.084-batch-extraction-pipeline.md)
- Missing `summary`: DP.M.085 (DP.M.085-personal-guide-onboarding.md)
- Missing `summary`: DP.M.087 (DP.M.087-secrets-map-pre-deploy.md)
- Missing `summary`: DP.M.089 (DP.M.089-f0-cost-baseline-llm-optimization.md)
- Missing `summary`: DP.M.090 (DP.M.090-ci-guard-mutation-testing.md)
- Missing `summary`: DP.M.091 (DP.M.091-scope-guard-parliament-enforcement.md)
- Missing `summary`: DP.M.092 (DP.M.092-infra-artifact-as-create-flow-step.md)
- Missing `summary`: DP.M.093 (DP.M.093-ci-artifact-in-create-flow.md)
- Missing `summary`: DP.M.094 (DP.M.094-dual-signal-ritual-gate.md)
- Missing `summary`: DP.M.095 (DP.M.095-atomic-cross-repo-terminology-sync.md)
- Missing `summary`: DP.M.096 (DP.M.096-property-graph-vs-triple-store.md)
- Missing `summary`: DP.M.097 (DP.M.097-lint-completeness-check.md)
- Missing `summary`: DP.M.098 (DP.M.098-premise-pain-probe.md)
- Missing `summary`: DP.M.099 (DP.M.099-illustration-as-pack-object.md)
- Missing `summary`: DP.M.100 (DP.M.100-vocabulary-sufficiency-gate.md)
- Missing `summary`: DP.M.105 (DP.M.105-workflow-call-orchestration.md)
- Missing `summary`: DP.M.107 (DP.M.107-role-rename-downstream-review.md)
- Missing `summary`: DP.M.108 (DP.M.108-specializes-vs-parallel-roles.md)
- Missing `summary`: DP.FM.004 (DP.FM.004-narrow-pregeneration-scope.md)
- Missing `summary`: DP.FM.015 (DP.FM.015-false-positive-capture-detection.md)
- Missing `summary`: DP.FM.016 (DP.FM.016-routing-config-path-decay.md)
- Missing `summary`: DP.FM.021 (DP.FM.021-zero-slot-blocks-min-aggregation.md)
- Missing `summary`: DP.FM.022 (DP.FM.022-systemd-minimal-path.md)
- Missing `summary`: DP.FM.023 (DP.FM.023-service-user-credentials-path.md)
- Missing `summary`: DP.FM.024 (DP.FM.024-git-pull-in-production.md)
- Missing `summary`: DP.FM.025 (DP.FM.025-monorepo-multisvc-f1-violation.md)
- Missing `summary`: DP.FM.026 (DP.FM.026-env-git-history-leak.md)
- Missing `summary`: DP.FM.031 (DP.FM.031-hardcoded-os-path.md)
- Missing `summary`: DP.FM.034 (DP.FM.034-pack-ciphers-in-guide-text.md)
- Missing `summary`: DP.FM.035 (DP.FM.035-ci-live-config-patch.md)
- Missing `summary`: DP.FM.041 (DP.FM.041-dedup-slice-false-positive.md)
- Missing `summary`: DP.FM.042 (DP.FM.042-same-schema-neon-dbs.md)
- Missing `summary`: DP.FM.043 (DP.FM.043-case-enum-assumption.md)
- Missing `summary`: DP.FM.044 (DP.FM.044-retroactive-backfill-regime-mismatch.md)
- Missing `summary`: DP.FM.047 (DP.FM.047-third-party-pii-vendor-gate.md)
- Missing `summary`: DP.FM.048 (DP.FM.048-cf-bot-fight-mode-xhr-block.md)
- Missing `summary`: DP.FM.049 (DP.FM.049-document-centric-bottleneck.md)
- Missing `summary`: DP.FM.050 (DP.FM.050-markdown-bold-regex-punctuation.md)
- Missing `summary`: DP.FM.051 (DP.FM.051-on-conflict-nullable-unique-incompleteness.md)
- Missing `summary`: DP.MAP.001 (DP.MAP.001.md)
- Missing `summary`: DP.SC.021 (DP.SC.021-mcp-knowledge-access.md)
- Missing `summary`: DP.SC.022 (DP.SC.022-personal-knowledge-indexing.md)
- Missing `summary`: DP.SC.023 (DP.SC.023-mcp-extensibility.md)
- Missing `summary`: DP.SC.024 (DP.SC.024-iwe-maintenance.md)
- Missing `summary`: DP.SC.025 (DP.SC.025-capture-bus.md)
- Missing `summary`: DP.SC.026 (DP.SC.026-agent-behavior-monitoring.md)
- Missing `summary`: DP.SC.027 (DP.SC.027-repo-touch-gate.md)
- Missing `summary`: DP.SC.031 (DP.SC.031-personal-read-api.md)
- Missing `summary`: DP.SC.032 (DP.SC.032-personal-data-view-audit.md)
- Missing `summary`: DP.SC.037 (DP.SC.037-agent-trace.md)
- Missing `summary`: DP.SC.038 (DP.SC.038-agent-replay.md)
- Missing `summary`: DP.SC.039 (DP.SC.039-multipath.md)
- Missing `summary`: DP.SC.040 (DP.SC.040-pattern-miner.md)
- Missing `summary`: DP.SC.131 (DP.SC.131-backup-process.md)
- Missing `summary`: DP.SC.140 (DP.SC.140-club-action-catalog.md)
- Missing `summary`: DP.SC.145 (DP.SC.145-llm-router.md)
- Missing `summary`: DP.SC.146 (DP.SC.146-secret-drift-detector.md)

---

*Generated by `scripts/generate-map.py` on 2026-05-20*