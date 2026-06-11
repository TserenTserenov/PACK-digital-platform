---
id: DP.MAP.001
name: Pack Navigation Map
scope: full-pack
created: 2026-06-11
last_updated: 2026-06-11
generated: true
---

# [DP.MAP.001] Pack Navigation Map

> Auto-generated from frontmatter on 2026-06-11. Do not edit manually.

---

## Statistics

| Kind | Count |
|------|-------|
| AISYS (AISYS) | 4 |
| ARCH (ARCH) | 9 |
| ASSIST (ASSIST) | 1 |
| CONCEPT (CONCEPT) | 3 |
| Distinctions (D) | 83 |
| ECON (ECON) | 1 |
| EXOCORTEX (EXOCORTEX) | 1 |
| Failure Modes (FM) | 136 |
| IWE (IWE) | 13 |
| KR (KR) | 2 |
| Methods (M) | 274 |
| Maps (MAP) | 2 |
| METHOD (METHOD) | 10 |
| NAV (NAV) | 1 |
| ONT (ONT) | 1 |
| ORG (ORG) | 1 |
| ROADMAP (ROADMAP) | 2 |
| ROLE (ROLE) | 59 |
| RUNBOOK (RUNBOOK) | 1 |
| SC (SC) | 126 |
| SoTA Annotations (SOTA) | 28 |
| SYS (SYS) | 1 |
| VM (VM) | 1 |
| Work Products (WP) | 16 |
| **Total** | **776** |

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
| DP.D.052 | Различение: Персона / Память / Контекст | Три слоя пользовательской модели — замена legacy-термина «ЦД». Критерий разделения = writer + owner (source-of-truth), не когнитивный и не по TTL. Персона = distributed-entity (identity-anchor + Git declarations + Neon refs), Память = platform-owned Neon, Контекст (= Проекция) = runtime-ephemeral. v2: разделены оси Writer / Identity-anchor / State-storage / Snapshot-unit (вместо склейки Owner+Артефакт); добавлено различение Носитель ≠ Персона ≠ Декларация Персоны (§10). | active |
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
| DP.D.079 | Smoke Technical Vs Processing Signal | — | active |
| DP.D.080 | Контрольная роль ≠ Операционная роль | — | active |
| DP.D.089 | Cascading failure ≠ Independent failures | — | active |
| DP.D.090 | Structural smoke ≠ E2E smoke (по типу данных) | — | active |
| DP.D.091 | Выровненные на boundary шкалы ≠ Параллельные с tandem-стыком | — | — |
| DP.D.092 | Rate limit ≠ Value: частотный потолок и ценность — две оси, две колонки | — | — |
| DP.D.093 | Метка классификатора ≠ Источник ошибки | — | — |
| DP.D.094 | Temporal correlation ≠ Causation | — | active |
| DP.D.095 | IWE ≠ Платформа — правило «кто управляет экземпляром» | — | active |
| DP.D.096 | Парламент-модель памяти агентов — 5 элементов и инварианты | — | active |
| DP.D.097 | Loop control у вызывающей роли, не у вызываемой | — | active |
| DP.D.098 | Ground truth ≠ Self-assessment для валидации proxy-моделей | — | active |
| DP.D.099 | Метрика чтения ≠ метрика downstream-эффекта | — | — |
| DP.D.101 | Shared Module Sharing: Symlink (α) ≠ Submodule (β) ≠ Vendor Copy (γ / γ-prime) | — | active |
| DP.D.102 | Четыре канала событий IWE по семантике | — | active |
| DP.D.103 | Специализация агента через контекст ≠ специализация через дообучение | Два уровня специализированного агента: уровень 1 — универсальное LLM-ядро + роль в контексте (Pack + промпт); уровень 2 — дообученное LLM-ядро, роль запечена в веса. Разные оси: где живёт доменное знание. | active |
| DP.D.104 | Прогресс к награде ≠ Показ баланса | — | — |
| DP.D.108 | Поведенческий ≠ Технический bottleneck | — | active |
| DP.D.109 | TOC Bottleneck (вклад в потерю Throughput) ≠ Readiness Gap (разрыв готовности) | — | active |
| DP.D.110 | Pillar-текст ≠ Conversion Post | — | active |
| DP.D.111 | Триаж ≠ Исполнение | — | active |
| DP.D.112 | Cutover инфраструктуры ≠ маркетинговый запуск | — | active |
| DP.D.113 | AND-семантика ≠ OR-семантика для multi-storage state | Когда состояние сущности разнесено между volatile + durable storage'ами: AND-семантика (активна если оба источника подтверждают) требует orphan recovery loop; OR-семантика (активна если хотя бы один) безопаснее для doubt cases. | active |
| DP.D.114 | Software factory ≠ Platform — single-product vs PaaS | — | active |
| DP.D.115 | Distributed orchestration ≠ Monolithic orchestrator | — | active |
| DP.D.116 | Semantic compiler ≠ Static site generator (SSG) | — | active |
| DP.D.117 | Два render pipeline'а ≠ два продукта ≠ два региона | — | active |
| DP.D.118 | N-мерная ортогональность ролей в peer-сессии | — | active |
| DP.D.119 | Предметная роль ≠ структурная роль в peer-сессии | — | active |
| DP.D.120 | Type-string runtime drift ≠ File-replace terminology drift | Два класса drift'а вокабуляра. Runtime: writer и resolver обмениваются через string literal без shared enum — новые значения silently попадают в else-ветку. File-replace: переименование термина в файлах через sed — пропущенные места остаются с old name. | active |
| DP.D.121 | ТОС-горлышко системы ≠ горлышко портфеля проектов | — | active |
| DP.D.122 | Continuous Trend Vs Point In Time | — | active |
| DP.D.123 | State-Dependency Test для классификации skills | — | active |
| DP.D.124 | Агент-персонаж ≠ Агент-рантайм | — | active |
| DP.D.125 | Два независимых измерения вместо матрицы (технологический тир ⟂ содержательный progress) | — | active |
| DP.D.126 | Интерфейс ≠ Тир (канал доставки ортогонален технологическому уровню) | — | active |
| DP.D.127 | Aux Class Vs Narrative | — | — |
| DP.D.128 | Статический промпт ≠ интерактивный канал | — | active |
| DP.D.129 | Historical Membership Vs Current Channel | — | — |
| DP.D.130 | Технологическая ось онбординга ≠ Содержательная ось | — | — |
| DP.D.131 | Костюм ≠ Оснащение (тир) | — | active |
| DP.D.132 | Первокурсник ≠ Участник сообщества (промежуточное состояние входа ≠ полная готовность) | — | active |

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
| DP.M.086 | Cheap idempotency: dedicated notification_log вместо ALTER TABLE column | — | active |
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
| DP.M.109 | Метод операциональной точности интеграционных терминов | — | active |
| DP.M.110 | Декларативный словарь предикатов для nudge-движка | — | — |
| DP.M.111 | Majority-vote детектор структурного drift | — | — |
| DP.M.112 | run_skill() — headless dispatch скиллов через claude -p | — | draft |
| DP.M.113 | Разделение earned_total и points в gamification схеме | — | draft |
| DP.M.114 | Исторический cap бонусов: интеграл по истории квалификации | — | draft |
| DP.M.115 | Конвейер руководств из Pack (Living Documentation CI/CD) | Pack = единый источник истины для N руководств. Изменение в Pack → автоматическая валидация структуры → оценка качества → сборка нового контента. Персонализация = дополнительный слой: разделы выбираются по ступени + bottleneck + домен пользователя. | active |
| DP.M.116 | Решение о распределении captures по Pack (Вариант B > Вариант A) | При KE из нового источника: предпочтительно распределить по существующим Pack (Вариант B), а не создавать новый Pack (Вариант A). Вариант A оправдан только при: (1) принципиально новый домен, или (2) ≥30% сущностей не вписываются ни в один существующий Pack. | active |
| DP.M.117 | Cohort Content As Declarative Json | — | active |
| DP.M.118 | Cohort Intake Survey Freeze | — | active |
| DP.M.120 | Boundary Mapping Constant — single source граничного маппинга | — | active |
| DP.M.121 | Universal Guide Phases F0 F6 | — | draft |
| DP.M.122 | Security Culture (Pilot habits) | — | draft |
| DP.M.123 | Backup (Pilot method) | — | draft |
| DP.M.124 | Encryption (Pilot method) | — | draft |
| DP.M.137 | Auto-Trigger Subagent Review on First Subsection | — | active |
| DP.M.138 | Dispatcher: синхронизация origin и идемпотентная запись результата после headless-агента | — | draft |
| DP.M.139 | Lint-плейсхолдер как детектор онтологических пробелов Pack | — | draft |
| DP.M.140 | Двухфазный жизненный цикл онтологических терминов: forming → formalized | — | draft |
| DP.M.141 | Выбор source в pack_refs: ID Pack vs docs + ontology_anchor | — | — |
| DP.M.142 | CI Setup Flag Mode Separation | — | draft |
| DP.M.145 | Terminology replace — multi-pass verify through peer agent | — | draft |
| DP.M.146 | Working-hypothesis marker with verification source | — | — |
| DP.M.147 | Semantic-first / Performance-later layered integration | — | draft |
| DP.M.148 | Audit cascade — обновление главного документа с прогоном связанных на drift | — | — |
| DP.M.149 | Bearer == Shared Secret Backward-Compatible Auth Mode | — | draft |
| DP.M.150 | Multi-Driver Compat via Duck-Typing of Connection API | — | draft |
| DP.M.153 | Scaffold Fallback — Minimal Valid Document (не пустой файл) | Else-ветка guard-блока в cascaded scaffold-системе создаёт минимальный валидный документ (frontmatter + комментарий generated_by: fallback), а не пустой файл через touch. Downstream-парсеры получают рабочую оболочку, а не падают на отсутствующем YAML-блоке. | draft |
| DP.M.154 | Embedded Python в bash — обязательные with-блоки (CPython-refcount-independence) | Embedded-Python сниппет в shell-скрипте для write-операций над manifest/config/state-файлами обязан использовать `with open(...) as f:` для каждого open. Безсонтекстный `json.dump(d, open(f, 'w'))` зависит от CPython refcount-driven __del__ — рискует partial-write на async/PyPy/exception. | draft |
| DP.M.155 | Raw GitHub Distribution Model (raw-main delivery — коммит в main = production, version — info label не gate) | Модель доставки template-системы через raw.githubusercontent.com/<owner>/<repo>/main/<path>. Любой коммит в main немедленно доступен пользователям при следующем update.sh. Версия в manifest — информационная метка, не gate. Цена: pre-merge CI становится единственным защитным барьером. | draft |
| DP.M.156 | Upgrade-Markers в Service Contract | — | — |
| DP.M.157 | CI-чек покрытия манифеста дистрибутива | — | — |
| DP.M.158 | Archgate Defer Pattern | — | — |
| DP.M.159 | Скилл как единственная исполняемая точка входа | — | — |
| DP.M.160 | Single point of degradation tracking | — | active |
| DP.M.161 | Pack-зрелость как параметр оценки трудозатрат | — | active |
| DP.M.162 | Adversarial Peer Review для методологических текстов | — | draft |
| DP.M.163 | Checkpoint-протокол для отложенной финализации фазы РП | — | draft |
| DP.M.164 | Base Group Replaces Domain Multiplier | Замена двойного кодирования ценности (domain_mult × base_group) на единственный base_group. Домен остаётся аналитическим атрибутом, не множителем в формуле начисления очков. | active |
| DP.M.165 | Soft streak reset — плавное снижение вместо обнуления | — | active |
| DP.M.166 | Referral-вознаграждение через ₽-кредит, не баллы | — | active |
| DP.M.167 | Ветвление refinement-промпта по длине предыдущего ответа | — | — |
| DP.M.168 | Post-deploy регрессия как гипотеза №1 в RCA | — | — |
| DP.M.169 | Экспериментальный вес с guard-условием для ML-метрики | — | active |
| DP.M.170 | Router-роль как рычаг разделения dispatch-решения от исполнения | — | active |
| DP.M.171 | Fpf Sync Delta Map | — | active |
| DP.M.172 | Knowledge File Archive Vs Delete | — | active |
| DP.M.173 | Artifact-first контракт agentic-роли с confidence-полем | — | — |
| DP.M.174 | Triple-hash idempotency для LLM-pipeline | — | — |
| DP.M.176 | WP Inbox: flat-file vs folder structuring | — | — |
| DP.M.177 | Управление жизненным циклом bug-report в inbox | Метод управляет жизненным циклом bug-report файлов в inbox/bugs/ через frontmatter-статус (open|resolved|invalid) и триггер Week Close: автоматический review открытых багов старше 14 дней с архивацией разрешённых. | active |
| DP.M.178 | Wp Triage Three Step Filter | — | active |
| DP.M.179 | Single Source Dashboard Script | — | active |
| DP.M.180 | Defer Policy No Auto Escalate | — | active |
| DP.M.181 | Multi Turn Session Thread Pattern | — | draft |
| DP.M.182 | Dual Sla Acknowledgment Completion | — | draft |
| DP.M.183 | Level Dependent Bonus Caps Ema | — | — |
| DP.M.184 | EMA-сглаживание курса бонусов | — | — |
| DP.M.185 | Степенная функция начисления баллов за усилие | — | — |
| DP.M.186 | Тест 15-секундного обещания onboarding | — | — |
| DP.M.187 | Бустер новичка: фиксированный множитель первые N дней | — | — |
| DP.M.188 | Маппинг N backend ступеней в M UI грейдов | — | — |
| DP.M.189 | Floor курса для защиты бизнес-обещания при росте community | — | — |
| DP.M.190 | 3-уровневый fallback для технического риска в live-демо | — | — |
| DP.M.191 | CTA воронки = ближайший продукт по времени, не самый ценный | — | — |
| DP.M.192 | C9-проверка: абстрактный термин → сцена с человеком в действии | — | — |
| DP.M.193 | Гибридный фикс — regex tolerance + локальная унификация | — | draft |
| DP.M.194 | Anchored regex для frontmatter-aware матчинга | — | draft |
| DP.M.195 | Pull-driven feature activation — defer до explicit user request | — | active |
| DP.M.196 | Upsert Runtime Verify Double Delta | — | proposed |
| DP.M.197 | Fix Contract (FC) — исполняемая спецификация исправления с regression_checks | — | — |
| DP.M.198 | Атомарный переход в degrade-state: state + user-reply одним PUT | — | — |
| DP.M.199 | Три уровня параметров конфигурируемой системы | — | active |
| DP.M.200 | Самофинансирующийся реферальный механизм | — | active |
| DP.M.201 | Separate API Keys per Workload (изоляция квот по рабочим нагрузкам) | — | active |
| DP.M.202 | Loyalty: отдельная группа community events с двумя независимыми лимитами | — | active |
| DP.M.203 | Neon multi-DB FDW cross-schema prefix rules | — | — |
| DP.M.205 | Gamification Rate Limit by Event Controllability | — | — |
| DP.M.206 | Fast-fail-and-restart предпочтительнее in-process reconnect когда состояние коннекта = source-of-truth подписки | — | active |
| DP.M.207 | Explicit choice до stateful default при первом входе | — | active |
| DP.M.208 | Diagnostics до behavioral nudge при stuck-сегменте | — | active |
| DP.M.209 | Dry-run = 50% production migration: полный checklist с явным блокером | — | active |
| DP.M.210 | Трёхуровневая сегментация застрявших пользователей (α/β/γ) для диагностики bottleneck | — | active |
| DP.M.211 | Диагностика L1 FAIL в concept-coverage по регистрационному зазору | — | active |
| DP.M.212 | Маппинг Discourse webhook в IWE event pipeline | — | active |
| DP.M.213 | UPSERT + xmax=0 — атомарное определение INSERT vs UPDATE | — | active |
| DP.M.214 | Silent OAuth Token Provisioning — провиженинг через session cookie | — | active |
| DP.M.215 | SQL NOT EXISTS guard для predicate-based row exclusion | — | active |
| DP.M.217 | Glue Requires Executor Pipeline Decomposition | — | active |
| DP.M.218 | Defense-in-depth протокола: Close-check + Open-autofix | — | active |
| DP.M.219 | BY-SCRIPT маркер — идемпотентная авто-инжекция в шаблонный файл | — | active |
| DP.M.220 | Threshold-or-time авто-коммит с daily squash | — | active |
| DP.M.223 | Marp тёмная тема — layout-классы для структурированных презентаций | — | — |
| DP.M.225 | Identity-anchor персонаж в семинаре | — | draft |
| DP.M.226 | Прогрессивное заполнение карточки в семинаре (3 точки) | — | draft |
| DP.M.230 | Двухуровневая защита async replay-loop от infinite retry (outer + per-event wait_for) | — | active |
| DP.M.231 | Одновременное восстановление N domain-rules как диагностический маркер блокировки main loop | — | active |
| DP.M.232 | Декомпозиция umbrella-РП: domain-specific subsystem ≠ standard infra direction | — | active |
| DP.M.233 | Cutover-date в детекторе вместо backfill legacy state | — | active |
| DP.M.234 | Двухусловное определение «открыто» для гигиены workflow-артефактов | — | active |
| DP.M.235 | Audit зонтичного РП: rescope через promote/cancel/defer/spawn | — | active |
| DP.M.236 | Разделение фазы РП по классу верификации (trivial/closed-loop/open-loop/problem-framing) | — | draft |
| DP.M.237 | Auto-route первого входа + explicit manual override affordance (SRB pattern) | — | draft |
| DP.M.238 | Pre-articulated open questions в отложенной problem-framing фазе | — | draft |
| DP.M.239 | Defense-in-depth bail-out при refactor regex single→multi: fail-loud вместо silent best-effort | — | active |
| DP.M.240 | Self-recoverable tooling: SoT в репо + symlink/copy в writable PATH | — | active |
| DP.M.241 | Порядок формирования персонального руководства | — | active |
| DP.M.242 | Ar5 Pack Quality Baseline | — | accepted |
| DP.M.243 | Discriminator Column Sti Pattern | — | — |
| DP.M.244 | Trust Boundary Server Side Authz | — | — |
| DP.M.245 | Cp Profile Adaptive Facilitation | — | — |
| DP.M.246 | Content Debt Triage Inbox | — | — |
| DP.M.247 | Pre-LLM Eligibility Gate | — | active |
| DP.M.248 | Composable CLI Linter — One Subcommand per Rule | — | active |
| DP.M.249 | Delivery Tracker — Living Navigation Artifact for Umbrella WP | — | active |
| DP.M.250 | Glossary-Driven Lint via YAML — Rules as Data | — | active |
| DP.M.251 | Nighttime Rollout with Pre-Deploy Rollback and Post-Deploy Verifier | — | active |
| DP.M.252 | Satisfied-by-Existing-Content — pre-build scout как класс defer в delivery pipeline | — | active |
| DP.M.253 | Seminar Orientation Map — max-impact triple для семинара с концептуальным контентом | — | active |
| DP.M.254 | Container abstraction mapping — IT-аналогии через Persona+Память+Контекст без импорта docker-терминов | — | active |
| DP.M.255 | Поликорневая сборка контекста | — | active |
| DP.M.256 | Pointer Only Fork Closure | — | active |
| DP.M.257 | Closed Partial Multi Channel Resumption | — | active |
| DP.M.258 | Cross Component Trigger Body Search Path | — | — |
| DP.M.259 | Resource constraint доминирует в портфеле при одном исполнителе | — | active |
| DP.M.260 | Intentional disablement как третья гипотеза при пустой/нулевой функции | — | active |
| DP.M.261 | Port working SQL из known-good источника vs реимплементация | — | active |
| DP.M.262 | Bidirectional cross-reference как защита от lifecycle coupling через чужой exec-механизм | — | active |
| DP.M.263 | Каскад Pack-расширения через ad-hoc → snapshot → audit → авто-WP | — | current |
| DP.M.264 | Пороговый сценарий аудита вместо отдельной операционной роли | — | current |
| DP.M.265 | Delta Signal Not Raw Values | — | active |
| DP.M.266 | Internal service auth: shared secret + X-User-ID header вместо user_jwt propagation | — | active |
| DP.M.267 | Grep Marker Deferred Auto Registry | — | — |
| DP.M.268 | Auto Generated Ownership Marker | — | — |
| DP.M.269 | Bidirectional Registry Drift Guard | — | — |
| DP.M.270 | Resolve Instructions Level | — | — |
| DP.M.271 | Lazy Channel Aware Resource Creation | — | — |
| DP.M.272 | Role Unpacking Via Split To | — | — |
| DP.M.273 | Explicit Prefix Guard Disambiguation | — | — |
| DP.M.274 | Три уровня мастерства пилота (Iron Man framing) | — | active |
| DP.M.275 | Sc Decomposition Via Umbrella | — | — |
| DP.M.276 | Add Not Rename On Unpacking | — | — |
| DP.M.277 | Single Source Method N Surfaces | — | — |
| DP.M.278 | Hybrid Corpus Audit Protocol | — | — |
| DP.M.279 | Held Patch Pattern | — | — |
| DP.M.280 | Allow Fallback Cutover Pattern | — | — |
| DP.M.281 | Recurring Error Diagnosis | — | active |
| DP.M.282 | Function First Onboarding | — | active |
| DP.M.283 | Byok First Tier Unlock | — | active |
| DP.M.284 | Inline Cat Over Add Dir Cli | — | — |
| DP.M.285 | Dual Write Safety Net Projection Migration | — | — |
| DP.M.286 | Cold Review Frontmatter Anchors Pass | — | — |
| DP.M.287 | Grace Window Overlapping Scheduled Jobs | — | — |
| DP.M.288 | Dual-nudge same-day re-engagement — два нуджа о практике в день доставки контента | — | active |
| DP.M.290 | Explicit next-step numbering — явный номер следующего шага вместо абстрактного "завтра" | — | active |
| DP.M.291 | Patch Object Vs String Path Mock | — | — |
| DP.M.292 | Tier Source Provenance | — | — |
| DP.M.293 | Graceful Degradation Secondary Db Timeout | — | — |
| DP.M.294 | Extraction Report Lifecycle Applied Archive | — | — |
| DP.M.295 | Digital Twin Derived Over Primitive | — | — |
| DP.M.296 | Diagnosis Drill Down All Weak Slices | — | — |
| DP.M.297 | Platform Specific Path From Params Yaml | — | — |
| DP.M.298 | Fail-closed scope sidecar: ранний парсинг + deny при недоступности сервиса | — | — |
| DP.M.299 | Rotation impact map: инвентаризация мест секрета до ротации | — | — |
| DP.M.300 | gh pr diff branch-on-branch: проверка реального scope PR через checkout | gh pr diff на ветке поверх feature-ветки показывает изменения обеих суммарно; реальный scope PR берётся через checkout + git log main..HEAD. | — |
| DP.M.301 | Sync source-of-truth → derived: edit-commit-push в SoT, derived read-only | Две копии одного файла, синхронизируемые односторонне: правки только в источнике через commit перед sync, производная read-only — иначе sync затирает правки незакоммиченным состоянием. | — |

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
| DP.FM.045 | log-after-success violation: idempotency-log записан ДО side-effect → retry невозможен | — | — |
| DP.FM.046 | Render-queue timeout — отсутствующий deadline на вызов подзадачи | Задание зависает в очереди навсегда, потому что воркер ждёт ответа от подзадачи без явного timeout. Диагностика: open-sessions log. Признак: задание в статусе «выполняется» дольше expected_max. | active |
| DP.FM.047 | Third Party Pii Vendor Gate | — | draft |
| DP.FM.048 | Cf Bot Fight Mode Xhr Block | — | active |
| DP.FM.049 | Document-centric analysis yields false bottleneck | — | active |
| DP.FM.050 | Markdown Bold Regex Punctuation | — | active |
| DP.FM.051 | On Conflict Nullable Unique Incompleteness | — | draft |
| DP.FM.054 | Linter-зелёный ≠ структура body-текста | — | — |
| DP.FM.055 | deprecated_files в manifest ≠ удалён из runtime-runner | — | — |
| DP.FM.056 | Deprecated Not Deleted Runner Out Of Sync | — | draft |
| DP.FM.057 | cp.iwe в контенте Guides 1-2 — нарушение bounded-context | Включение cp.iwe (Machine-level competence, ступень 3+) в контент Guides 1-2 (ступени 1-2) создаёт скрытую зависимость: пользователь не может освоить базовый материал без навыков, которых у него ещё нет. | active |
| DP.FM.058 | Pilot-инсталляция с открытым дефолтом = silent PII-accumulation | — | active |
| DP.FM.059 | Hook Command Relative Path | — | draft |
| DP.FM.060 | Half Migration Manifest Runner Split | — | draft |
| DP.FM.061 | Ci Optional Secret Hard Fail | — | draft |
| DP.FM.070 | Dispatcher Git Reset Race Condition | — | active |
| DP.FM.072 | Не-канонические формы понятий в introduces и pack_refs | — | — |
| DP.FM.073 | Protocol Coverage Gap Mentioned Not Enforced | — | draft |
| DP.FM.074 | State-machine callback handler without router wire-up = silent dead-end | — | — |
| DP.FM.075 | deprecated-files-as-todo-tracker | Запись артефакта в `deprecated_files` до удаления всех зависимостей в коде — превращает список устаревших в TODO-трекер, что вызывает runtime-drift при следующем update. | draft |
| DP.FM.077 | Overstated Validator Coverage in Documentation (Документация заявляет автоматическое покрытие, которое не выдержано) | Документация валидатора/линтера/детектора заявляет 'автоматически ловит этот класс ошибок' без указания scope. Реально детектор покрывает только subset (например, regex по конкретным путям). Пользователь полагается на автоматику для всего класса → дрейф проходит мимо. | draft |
| DP.FM.078 | Ghost canonical pointer | — | active |
| DP.FM.079 | impact_group как множитель — математический взрыв в формуле вознаграждения | — | active |
| DP.FM.080 | Закрытие РП после первого фикса при многодефектном симптоме | — | — |
| DP.FM.081 | Double-count в probe-пути: одно событие → два инкремента деградации | — | — |
| DP.FM.082 | «4 кирпича = Президент» — Парламент-антипаттерн с единым посредником | — | active |
| DP.FM.083 | Empty Field Url Injection | — | active |
| DP.FM.084 | OAuth+CDN миграция без redirect_uri pre-flight: полный outage вместо частичного | — | — |
| DP.FM.085 | Hook-installer anti-patterns: --no-verify, double-run, no-backup, no-diff-check | — | — |
| DP.FM.086 | Dangling Intent: РП pending без dueDate | — | active |
| DP.FM.087 | Watchdog false-positive: молодой скрипт как overdue | — | active |
| DP.FM.088 | Done-фаза с открытыми чек-боксами — скрытый технический долг | — | active |
| DP.FM.089 | Test Blast Radius Shared Flow Io | — | proposed |
| DP.FM.090 | Числовой порядковый guard в multi-producer turn-log вместо семантического | — | — |
| DP.FM.091 | God-Table Anti-Pattern (склейка несвязанных доменов в core-таблице) | — | active |
| DP.FM.092 | Fire-and-forget temporal coupling со streak/бизнес-логикой | — | active |
| DP.FM.093 | Retry storm guard создаёт orphaned content при деградации API в момент первой попытки | — | active |
| DP.FM.094 | Бинарный счётчик advance маскирует легитимные причины non-advance (DLQ-blocked) | — | active |
| DP.FM.095 | Feature-flag activated without ALTER FUNCTION | — | — |
| DP.FM.096 | Config without emitter — invisible zero events | — | — |
| DP.FM.097 | Deployment Path Drift — Home vs Repo | — | — |
| DP.FM.098 | SM-Mutex Guard Coverage Gap — Queue-Based Flows Bypass Guard | — | — |
| DP.FM.099 | NOTIFY-подписка живёт на коннекте — смерть conn = весь event-loop зомби | — | active |
| DP.FM.100 | Snapshot stale → неверный диагноз без сигнала | — | — |
| DP.FM.101 | Rule-engine NOOP при отсутствии записи — silent event drop | — | active |
| DP.FM.102 | Boolean flag с hardcoded константой в ветке вычисления — silent underpayment | — | active |
| DP.FM.103 | Coverage-скрипт без фильтра scope агрегирует FAIL из соседних guide | — | draft |
| DP.FM.104 | Отсутствие обратной функции identity-lookup | — | active |
| DP.FM.105 | Внутренний health-probe слеп к собственным падениям | — | active |
| DP.FM.106 | Anthropic API usage limit — терминальный blocker automation-pipeline | — | active |
| DP.FM.107 | Volatile Function Upsert Trigger Cascade | — | — |
| DP.FM.108 | Owner-резолвер с пустым default из единственного источника (adopted-sovereign trap) | — | active |
| DP.FM.109 | Sentinel empty-string → прошлый слот планировщика | — | — |
| DP.FM.110 | Unix socket без protocol handshake → пустой ответ | — | — |
| DP.FM.111 | Спящее правило в session-memory: trust < cut-off → не попадает в reminders | — | active |
| DP.FM.113 | Regex `search()` глотает второе нарушение в multi-violation validators | — | — |
| DP.FM.114 | Adapter Dependency Silent Regression | — | accepted |
| DP.FM.115 | Peer Agent Overwrite Without Read | — | — |
| DP.FM.116 | External Id Path Traversal | — | draft |
| DP.FM.117 | Двойной учёт компонента в compound-формуле | — | — |
| DP.FM.118 | Двойное значение метрики в названии (theoretical vs operational) | — | — |
| DP.FM.119 | Concurrent Writers Break Threshold Logic | — | active |
| DP.FM.120 | Маскировка нулей вместо root-fix в диагностике метрик | — | active |
| DP.FM.121 | Dry-run side-effect — нарушение read-only обещания | — | active |
| DP.FM.122 | Spec без impl — спецификация ушла вперёд кода | — | active |
| DP.FM.123 | Reverse proxy режет long-running HTTP-handler — config application-timeout врёт | — | — |
| DP.FM.124 | lru_cache для async resource с lifecycle: leak + cross-loop errors | — | — |
| DP.FM.125 | Short-name fallback в authorization scope-check: cross-tenant bypass | — | — |
| DP.FM.126 | Полиморфный return type на shared helper ломает downstream callsites молча | — | — |
| DP.FM.127 | Python 3.9: тип-аннотации → TypeError без from __future__ import annotations | — | — |
| DP.FM.128 | Pytest: тест не запускается из-за ImportError при collection (Python ≤3.9) | — | — |
| DP.FM.129 | Broken Symlink Silent Config Empty | — | — |
| DP.FM.130 | Os Expanduser No Shell Vars | — | active |
| DP.FM.132 | Microservice Tier Sot Mismatch | — | — |
| DP.FM.133 | Backup Restore No 3Way Merge | — | active |
| DP.FM.134 | Vocabulary Split Aux Subsections | — | — |
| DP.FM.135 | Projection Rule No Backfill Fallback Mask | — | — |
| DP.FM.137 | Asymmetric Alert Suppression Paths | — | — |
| DP.FM.138 | Shared Db Without Env Discriminator | — | — |
| DP.FM.139 | Llm Proxy Default Timeout Too Short | — | — |
| DP.FM.140 | Cutover отключает основной путь, оставляя side-channel активным | — | — |
| DP.FM.141 | Shared queue без tenant-ключа: dedup-scope распространяется между инстансами | — | — |
| DP.FM.142 | New codepath no retry-symmetry — новый code-path без retry-симметрии с legacy-path | — | active |
| DP.FM.143 | Ppid Fallback Stale Pidfile Multiagent | — | — |
| DP.FM.144 | Side Effect Check Blocks Primary Flow | — | — |
| DP.FM.145 | FDW-только-READ: cross-DB write в SQL-миграции молча провалится | — | — |
| DP.FM.146 | Unconditional helper return = always-fires gate: гейт срабатывает для всех пользователей | — | — |
| DP.FM.147 | aiogram Bot() без try/finally session.close() → leak HTTP-коннектов в scheduler | Bot() создаётся per-call в scheduler, session.close() стоит после падающих операций без try/finally — при исключении HTTP-соединение к Telegram остаётся открытым, дескрипторы растут. | — |
| DP.FM.148 | Regex Detector Semantic Blindspot | Regex-детектор стиля видит только морфологию, не смысл — ловит одно орфографическое правило (98% срабатываний), семантические нарушения не замечает, создавая видимость покрытия. | — |
| DP.FM.149 | Channel Style Bleed Peer Synthesis | Синтезатор читает технические turn-файлы и продолжает их стиль при записи отчёта для пилота — английские термины и машинные маркеры переползают из доказательного слоя в pilot-facing. | — |
| DP.FM.150 | Silent Rule Decay No Cost | Детектор пишет лог, агрегатор поднимает напоминание по порогу N в неделю — при редких нарушениях критического правила порог молчит, нарушитель не видит ошибку, правило перестаёт действовать. | — |
| DP.FM.151 | Subscription gate multi-path divergence | В OAuth с двумя типами токенов (JWT и opaque) проверка подписки дублируется в нескольких путях кода — фикс одного пути не покрывает другой, один тип клиента проходит, другой блокируется при том же тарифе. | — |

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
| DP.SOTA.028 | Claude CLI headless hook inheritance — хуки из settings.json наследуются при `claude -p` | Lifecycle-хуки Claude Code (PostToolUse, Stop из .claude/settings.json) срабатывают при `claude -p` идентично интерактивному режиму. Headless-агент автоматически получает весь hook-слой (WakaTime, agent-trace-recorder, rule-engine) без дополнительного кода, при условии что CLAUDE_CONFIG_DIR / CLAUDE_PROJECT_DIR указаны. | draft |
| DP.SOTA.029 | Ai Era Two Crisis Groups | — | draft |

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
| DP.ARCH.002 | Тиры платформы | 4 оси полномочий: T0–T4 (учащийся) + TM1–TM3 (наставник) + TA1–TA4 (администратор) + TD1 (разработчик). Каждый тир — конфигурация среды по 5 измерениям. Оси полномочий ортогональны: один человек = T + TM? + TA? + TD?. Отдельно — 2 оси онбординга (оснащение × развитие), см. §2б. | draft |
| DP.ARCH.003 | Архитектура Digital Twin — единая точка расчёта и чтения | 8 принципов разделения Calculator / Reader. Единственный калькулятор — R28 Profiler. Интерфейсы — stateless витрины. Каждая цифра трассируется к IND-коду метамодели. | active |
| DP.ARCH.004 | Архитектура данных Neon (Database-per-BoundedContext) | 12 баз данных Neon по принципу database-per-BoundedContext. Сводная таблица, карта, ERD по каждой БД, связи, потоки, реестр физ.объектов с маркерами О/С/Р/К, revenue-sharing механика (контракты/сплиты/выплаты), points-ledger (event-sourcing) + эмиссионный отчёт, верификация по чеклистам SPF.SPEC.005, замечаниям Андрея Д1-Д12 и категориям WP-257. | active |
| DP.ARCH.005 | Персона (декларативная модель созидателя) | Персона — distributed-entity, представляющая одного носителя (человека) в IWE. Composition: identity-anchor (Ory subject_id или Pre-Grant claim_token) + declarations (Git PACK-personal/DS-my-strategy/captures) + refs (Neon persona_grants). Писатель деклараций = пользователь (или агент по его поручению с acceptance); identity-anchor издаётся системой регистрации. Заменяет часть монолита ЦД (DP.ARCH.003). v1.1 (2026-05-31): добавлен §0 — Носитель ≠ Персона ≠ Декларация + lifecycle anchor. | approved |
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
| DP.IWE.008 | BYOB (Bring Your Own Base) | — | draft |
| DP.IWE.009 | IWE Perimeter (Контур IWE) | — | draft |
| DP.IWE.010 | IWE Machine (Машина IWE) | — | draft |
| DP.IWE.011 | IWE Runtime Host Contract | — | draft |
| DP.IWE.011-adapter-claude-code | Claude Code Adapter for IWE Host Contract | — | active |
| DP.IWE.011-adapter-headless | Headless Adapter for IWE Host Contract | — | active |

### KR

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.KR.001 | Маршрутизация знаний IWE | Полная карта маршрутизации: какой тип контента куда записывать — от ZP до memory/, от Pack до 0.9.Inbox. Единый source-of-truth для агента и пользователя | draft |
| DP.KR.030 | Принцип триады учёт-доступ-аудит | Три функции институционального контроля — Учёт, Доступ, Аудит — должны быть структурно разделены. Совмещение любых двух из трёх в одной роли нарушает принцип независимости контроля. KR.030 = foundation серии; KR.031–033 = отдельные принципы каждой ветки (WP-214 Ф4). Серия KR.030–039 зарезервирована. | draft |

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
| DP.METHOD.051 | n8n встроенный /healthz endpoint для внешнего мониторинга | — | — |
| DP.METHOD.053 | Метод извлечения НЭП (Неудовлетворённость / Эмоция / Проблема) | Сократически-структурированный разбор сырых заметок и рефлексии на триаду Проблема / Неудовлетворённость / Эмоция с привязкой к роли и силе, выводящий пилота к целям и приоритетам месяца. Единый источник (single-source структуры) для обоих каналов discovery R1 Стратега — локального skill и серверного multi-turn. | active |

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
| DP.ROLE.012 | Стратег (Strategist) | Роль Стратег (R1) — стратегирование (WHAT/WHY): discovery неудовлетворённостей, диагностика состояния, приоритеты месяца. Операционное планирование (неделя/день) передано Плановику (DP.ROLE.066), РП378. | draft |
| DP.ROLE.012.SC.01 | 01 Strategy Session | Еженедельная сессия стратегирования (strategy_day 7:00): ревью НЭП, анализ прошлой недели, сдвиг фокуса месяца, формирование плана на неделю | active |
| DP.ROLE.012.SC.02 | — План дня | Ежедневное планирование (7:00): апдейт вчера по коммитам, контекст недели и план дня с рекомендацией старта | draft |
| DP.ROLE.012.SC.03 | 03 Week Review | Итоговое ревью недели (вс 22:00): агрегация дневных планов, анализ коммитов, расчёт статистики и публикация в клуб | draft |
| DP.ROLE.012.SC.04 | 04 Month Report | Итоговый отчёт за месяц: агрегация недельных данных, проверка выполнения приоритетов, анализ трендов и достижений | draft |
| DP.ROLE.012.SC.05 | 01 Evening Review | Вечерний итог дня по запросу: сопоставление коммитов со статусами РП, выявление незапланированного, carry-over на завтра | draft |
| DP.ROLE.012.SC.06 | 02 Check Plan | Сверка задачи с планом по запросу: классификация на in-plan / aligned / unplanned / urgent с рекомендациями действия | draft |
| DP.ROLE.012.SC.07 | 03 Update Priorities | Изменение приоритетов на уровне дня/недели/месяца: определение типа изменения, каскадные эффекты, diff и коммит | draft |
| DP.ROLE.012.SC.08 | 04 Add Workproduct | Добавление нового РП в план: сбор атрибутов, проверка бюджета, определение уровня размещения и коммит в план | draft |
| DP.ROLE.012.SCENARIOS | 00 Scenarios Index | Индекс и навигация по 8 сценариям Стратега: 4 по расписанию и 4 по запросу, с временной сеткой и потоком данных | draft |
| DP.ROLE.013 | Проводник (Conductor) | FSM-апдейтер доступного функционала. Получает сигнал о достижении пилотом условий открытия (подписка + N дней + ступень) и обновляет состояние доступных команд/кнопок/скиллов. Не принимает решение о готовности — решение принимает Контролёр развития (DP.ROLE.046). Не оценивает — оценивает Аттестатор (DP.ROLE.041). Не является стратегической доменной ролью — это инфраструктурный агент (infrastructure-agent) для feature unlocking T1→T4. | draft |
| DP.ROLE.022 | Оркестратор (Orchestrator) | Координатор цикла персонального развития: решает ЧТО и КОГДА запускать, делегирует исполнение специализированным Контролёрам и операционным ролям. На уровне суперсистемы координирует Контролёров (DP.ROLE.046 и его специализации); ниже — взаимодействует с Портным, Навигатором, Диагностом, Аттестатором, Проводником. | draft |
| DP.ROLE.023 | Верификатор (R23) | Sub-agent роль проверки артефактов по эталону (Pack/SPF/чек-лист) с context isolation. Возвращает PASS/FAIL с обоснованием. Не правит проверяемое, не выносит решение о допуске. | active |
| DP.ROLE.024 | Аудитор | Роль контроля полноты покрытия Pack'ов и DS-артефактов: сканирует целевое множество по индексу, выявляет gap'ы методами VR.M.002 (кросс-контекст) и VR.M.004 (полнота), формирует отчёт coverage % для заказчика. Инвариант: методологическая независимость (context isolation + read-only + формальный метод) — не организационная дистанция. Семейство: Контрольные (VR), маппинг VR.R.002. | draft |
| DP.ROLE.031 | Терминолог | Роль Терминолог отвечает за качество терминологии Pack: выбор переводов, онтологическое сопоставление с FPF, разрешение конфликтов имён. | draft |
| DP.ROLE.032 | Event Ingester | Роль единого приёмника доменных событий обучения от всех источников — гарантирует идемпотентность, валидацию и защиту от PII на входе в learning.domain_event | draft |
| DP.ROLE.033 | Редактор контента | Роль, читающая черновики автора и выдающая рекомендацию топ-3 в Day Open на основе актуальности и готовности. | draft |
| DP.ROLE.034 | Rewards Projector | Роль проектора баллов: читает learning.domain_event, применяет reference.reward_rules через compute_effective_amount, пишет в rewards.point_balances идемпотентно через cursor | draft |
| DP.ROLE.035 | Platform Observer | Роль наблюдателя за здоровьем платформы — оркеструет Better Stack (external observability owner), AIST Bot (TG-алерты команде + автопостинг канал), Neon `health.internal_metrics` (узкая projection для JOIN с business). | draft |
| DP.ROLE.036 | Коннектор клуба | Носитель потока данных systemsworld.club (Discourse) → Neon. Read-only ingest активности участников через webhook + polling backfill, с lazy-резолвом discourse_user_id ↔ ory_identity_id после ORY-SSO. | draft |
| DP.ROLE.037 | Регистратор РП | Координатор целостности: гарантирует, что статус любого РП одинаков во всех 5 хранилищах IWE. Не исполняет работу по РП — исполняет работу ПО МЕТАДАННЫМ РП. | active |
| DP.ROLE.038 | MCP Tool Consumer | Посредник между LLM-клиентом (бот) и платформенными MCP-серверами: загружает актуальный список tool через discovery (tools/list), кэширует с TTL, фильтрует по tier, передаёт в Claude API без hardcoded списков в коде. | draft |
| DP.ROLE.039 | Peer Agent (равноправный peer-агент в multi-agent сессии) | Peer-агент в multi-agent IWE сессии: работает в одном из двух режимов — (A) workspace-координация через Local Gateway lock + peer-status, (B) conversational-сессия через журнал реплик с позициями писатель/напарник. Конкретные инстансы: Claude Code, Kimikode, Aider и т.п. | draft |
| DP.ROLE.040 | OAuth Orchestrator (единая точка OAuth-flows для всех каналов IWE) | Сервис-роль: принимает OAuth setup/callback запросы от web/vscode/bot каналов, разрешает identity (Ory > telegram > github), управляет state-token lifecycle, координирует token exchange с провайдерами (GitHub App, Linear, Twin, Google Cal, WakaTime, Ory), хранит токены encrypted-at-rest в Neon. Не зависит от bot process. | draft |
| DP.ROLE.041 | Аттестатор | Роль автоматического вычислителя ступени Ученика: читает события из Activity Hub, считает 7 bh-характеристик (bh.sys/inv/met/awr/agn/scl/stb) по двум осям (Мастерство × Мировоззрение), сравнивает с нормативной матрицей и записывает bh-сигнал в learning.stage_transitions. Итоговую ступень фиксирует двойной gate: bh-сигнал Аттестатора + cp-подтверждение Диагноста (MIM.R.009). Болид-онтология: Аттестатор измеряет Пилота, не всего Созидателя. | draft |
| DP.ROLE.042 | Диагност (R28) | Роль диалоговой и фоновой диагностики ученика: проводит диалог ≤5 вопросов (три фазы), вычисляет cp-профиль (ступень + bottleneck + recommended_stream + skip_to_stage), сохраняет в learning.cp_assessments. Является стартом содержательной оси онбординга (DP.ARCH.002 §2б): диагностика доступна на T1 (free), результат питает get_journey_state (MCP). В фоновом режиме — silent-monitoring сигналов инвалидации и подсказки активным ролям (Навигатор / Портной / Аттестатор). Реализует двойной gate FORM.089 §5.1 с Аттестатором. | draft |
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
| DP.ROLE.053 | R29 Декомпозитор | — | active |
| DP.ROLE.054 | R30 Аналитик ограничений | Носитель методики TOC (Goldratt Five Focusing Steps + Tendon TameFlow Replenishment Cycle + Dettmer Thinking Processes). Идентифицирует систему-конвейер, сканирует функциональные обещания (SC-first), находит ограничение, выбирает TOC-инструмент и выдаёт план работы как карту этапов с зависимостями (без дат/часов). Универсален: применим к учебному конвейеру пилота, конвейеру работ (РП/эпик/проект/репо), когортному конвейеру. | draft |
| DP.ROLE.055 | Агент поддержки IWE | Носитель ответа на пилотские обращения через Chatwoot CE: маршрутизирует тикеты по теме (баг → разработчик, баллы → диспетчер, руководство → методист), отвечает в Chatwoot, эскалирует в Linear, поддерживает FAQ и Saved Replies. Граница: НЕ диагностирует архитектурные баги (это R6 Кодировщик), НЕ принимает продуктовые решения по фичреквестам (это Стратег R1 + пилот). | draft |
| DP.ROLE.056 | R32 Мейнтейнер скиллов | Владеет каталогом скиллов IWE. Принимает решения о promote L3→L1, отвечает за обратную совместимость при обновлении L1-скиллов, управляет жизненным циклом скилла (active→experimental→deprecated). | draft |
| DP.ROLE.057 | R33 Автор скилла | Создаёт новый скилл IWE через конвейер: create-skill.sh → SKILL.md v2 заполнить → validate-skill.sh → smoke-test → propose promote к Мейнтейнеру. Декларирует зависимости, выбирает layer (L1/L3), ссылается на DP.SC. | draft |
| DP.ROLE.058 | R?? Артефактор-Постановщик | Агентная роль: превращает сырой запрос пользователя в структурированный РП с routing-тегом (task_type + class), готовый к lookup в executor-catalog.yaml Маршрутизатора. | draft |
| DP.ROLE.059 | R30 Маршрутизатор | Единая точка маршрутизации задач IWE: получает запрос с routing-тегом, выбирает исполнителя из executor-catalog.yaml, не классифицирует самостоятельно — исполняет routing-решения WP Gate или Артефактора. | draft |
| DP.ROLE.060 | Презентатор | Роль, готовящая и проводящая публичные выступления (доклады, презентации) от имени IWE/MIM. Обеспечивает единый стиль, структурный каркас слайдов и воспроизводимый процесс подготовки. | draft |
| DP.ROLE.061 | External Session Adapter | Мост между внешним каналом (Telegram) и локальным исполнителем (Claude Code). Поддерживает multi-turn диалог: каждый ход дописывается в SESSION-thread, Egress запускает Claude Code с полным контекстом. Capability scope: код+git, calendar, WP, IWE-знания. Две sub-responsibility: Ingress (cloud) и Egress (local). | draft |
| DP.ROLE.062 | Создатель паков (R30) | Роль LLM-сопровождения автора PACK-X через SPF-цикл наполнения 01-11: вызывает R28 Диагност для определения режима (assembly/hybrid/full SPF), ведёт по фазам, защищает инвариант read-only upstream FPF/SPF. Работает с одним PACK-X за сессию; cross-pack consistency — у R24. | draft |
| DP.ROLE.063 | Менеджер оргразвития (R31) | Роль LLM-проводника между запросом субъекта об оргизменении (себя/команды/организации) и методами СИ/СМ/ИЛ программы РР. Шаг 0 — классификация типа системы (MIM.M.030). LLM-stateless по in-memory, file-stateful по контексту субъекта (personal-guide/team-guide). | draft |
| DP.ROLE.064 | Сторож новых задач (issue watcher) | Специализированная операционная роль: фоновый скрипт, который ежедневно обходит github-репо IWE, выявляет новые задачи (issues), классифицирует важность и шлёт дайджест пилоту в Telegram. Скрипт ≠ агент (фиксированный flow, без LLM). Один исполнитель = одна роль (специализированный агент по имени роли). | draft |
| DP.ROLE.065 | hermes-proxy-tool | — | draft |
| DP.ROLE.066 | Плановик (Planner) | Роль операционного планирования (HOW MUCH / WHEN): упаковка приоритетов месяца от Стратега (R1) в рабочие продукты недели с бюджетами, распределение по дням, удержание WIP-лимита. Выделена из R1 Стратега (DP.ROLE.012), который сужается до стратегирования (WHAT/WHY). | draft |
| DP.ROLE.067 | Онбордер | — | draft |
| DP.ROLE.068 | Постановщик задачи IWE | Член команды T4+. Превращает сырую нужду (баг, идея, замечание) в оформленную задачу для конвейера WP-403 с тегом маршрутизации, классом верификации и критерием приёмки. | draft |
| DP.ROLE.069 | Архитектор конвейера IWE | Член команды T4+. Проходит ArchGate и IntegrationGate для задач конвейера WP-403: обещание, сценарии, роли, границы. Сложные решения — согласование с Ведущим (TD1+TA4). | draft |
| DP.ROLE.070 | Верификатор конвейера IWE | Член команды T4+ (другой разработчик). Независимая проверка работы Разработчика-исполнителя по эталону перед закрытием РП. Возвращает PASS/FAIL с обоснованием. | draft |
| DP.ROLE.071 | Ведущий разработчик IWE | Ведущий разработчик команды IWE (TD1+TA4). Согласовывает merge, принимает архитектурные решения высокого уровня, подписывает рост в команде. | draft |
| DP.ROLE.072 | Разработчик-исполнитель IWE | Член команды разработки IWE (T4+ / TD1). Ведёт задачу через 6 станций конвейера WP-403, обеспечивая двойной выход: работающий код/артефакт + зафиксированное знание. | draft |
| DP.ROLE.073 | Хранитель реестра стилей | — | draft |
| DP.ROLE.074 | Диспетчер стилей | — | draft |

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
| DP.SC.011 | Стратегирование и Планирование | Зонтичное обещание сквозного цикла: неудовлетворённости → приоритеты (стратегирование) → утверждённый план недели (планирование). Реализуется двумя ролями (Стратег + Плановик) через DP.SC.030 + DP.SC.051. | draft |
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
| DP.SC.030 | Разговор-распаковка неудовлетворённостей (discovery-стратегирование) | R1 Стратег ведёт пилота сократическим диалогом от сырой рефлексии и заметок к структурированным неудовлетворённостям, состоянию и приоритетам месяца (WHAT/WHY), не подсказывая формулировки за него. | active |
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
| DP.SC.045 | Анализ ограничения системы (TOC) | Потребитель (пилот / Стратег / Декомпозитор / Навигатор) получает на выходе пятифазного ВДВ-каскада три артефакта: System Card (классификация системы-конвейера), Constraint Brief (описание ограничения с trichotomy + class), Stage Dependency Map (план работы как dependency graph без дат и часов). SC-first: первой проверяется работоспособность функциональных обещаний, не структура pending-РП. | draft |
| DP.SC.046 | Runtime-цикл IWE (open → work → close) | IWE гарантирует, что любая рабочая сессия проходит через три обязательные фазы — open, work, close — независимо от хоста (Claude Code, Hermes runtime, бот). Контракт хост-агностичный: протоколы определяются слоем 4, не слоем 3. | draft |
| DP.SC.047 | Презентация к публичному событию | Подготовка и проведение публичного выступления (доклада, презентации) с единым стилем IWE/MIM. Вход — тема и событие, выход — Marp-слайды + PDF + отчёт + post-deck пакет. | draft |
| DP.SC.048 | Создатель паков | Автор PACK-X получает: LLM-сопровождение через весь SPF-цикл наполнения собственного Pack 01-11, с режимом по cp-профилю (assembly/hybrid/full SPF) и защитой инварианта read-only upstream FPF/SPF. | draft |
| DP.SC.049 | Менеджер оргразвития | Пилот (как субъект изменения себя/команды/организации) получает: классификацию типа системы (личность/команда/организация), проверку applicability, выбор метода (СИ/СМ/ИЛ) и конкретный первый шаг ≤30 мин из одного из канонических руководств программы РР. | draft |
| DP.SC.050 | Единый разговорный стиль агентов | Каждый агент (Claude, Kimi, Hermes) получает единую базу разговорного стиля и исполняет её при общении с людьми | active |
| DP.SC.051 | Совместный недельный ритуал Стратега и Плановика | Недельное планирование — совместный ритуал R1 Стратега (DP.ROLE.012) и Плановика (DP.ROLE.066): Плановик ведёт упаковку приоритетов в неделю; Стратег подключается, только если приоритеты устарели или состояние пилота изменилось. | active |
| DP.SC.052 | vdv-skill | Генерирует описание стадийного процесса по методу ВДВ или проверяет готовое описание по 6 принципам сцепки входов-выходов | active |
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
| DP.SC.151 | Контролёр развития (профиль Onboarding Tick) | Опт-инный пилот R2 получает поведенческий нудж (TG или render-задача Портному) по очереди из 11 онбординговых сообщений (WP-343) + независимые upgrade-маркеры T1→T4 (WP-349: B-low/B-high/C/E). Сообщение приходит не по расписанию, а по реальному поведению пилота. Не более 1 нуджа в сутки. Следующее сообщение доставляется в течение 8h после срабатывания триггера. | active |
| DP.SC.152 | Анализ ограничения ИТ-платформы (platform-bottleneck) | Стратег или CTO получает Constraint Brief с конкретной C2-подсистемой из MAP.002, где максимальное число failing SC, + Stage Dependency Map для устранения. Отличие от SC.045: target жёстко ограничен C2 ИТ-платформой, SC-scan идёт по MAP.002 (12 подсистем, SC.001-SC.151), не по произвольному конвейеру. | draft |
| DP.SC.153 | Скилл-система IWE | Разработчик IWE получает: каталог всех скиллов с метаданными и графом зависимостей; конвейер создания (create-skill.sh → validate → promote); безопасное обновление через versioning без перезаписи L3-кастомизаций. | draft |
| DP.SC.154 | Мульти-агентная диалоговая сессия | Пилот ставит задачу команде из 2+ peer-агентов разных вендоров; они многотурово обсуждают её, согласуют единый отчёт; любой может эскалировать к пилоту при принципиальном несогласии. | draft |
| DP.SC.155 | Маршрут оснащения (Setup Journey) | Пилот R2 на T1 открывает /setup и получает актуальный дашборд прогресса по пути T1→T4: текущий тир, ступень мастерства, что подключено, следующий шаг с CTA. Дашборд читает свежие данные (tier_detector + cp_assessments + onboarding_state) через asyncio.gather. Guided flow проводит шаг за шагом без повторных нажатий (double-tap protection). Последнее CTA-действие пишет last_nudge_at — предотвращает дубль от onboarding_controller в течение 24h. | draft |
| DP.SC.156 | Обнаружение возможностей уровня (Что ещё?) | Пользователь T1-T4 получает список доступных команд своего уровня одним нажатием из tier-экрана | draft |
| DP.SC.157 | Оптимизированный вход в марафон | T1-пользователь получает первый урок марафона за 4 действия от /start, без ручного ввода команд | draft |
| DP.SC.158 | Канон tier-сообщений бота | Пользователь T1-T4 видит единообразное сообщение об уровне по шаблону с номером тира и описанием доступного | draft |
| DP.SC.159 | Маршрутизатор задач IWE | Пилот или агент получает: единственного исполнителя для любой входящей задачи — детерминированного (скрипт) или рассуждающего (LLM/скилл) — в соответствии с routing-тегом, проставленным WP Gate или Артефактором. | draft |
| DP.SC.160 | Артефактор-Постановщик задач IWE | Пилот или Маршрутизатор получает: из сырого запроса — структурированный РП с routing-тегом (task_type, class, artifact, budget_estimate), готовый к lookup в executor-catalog. | draft |
| DP.SC.161 | Session Memory Injector | Pre-flight сервис: читает iwe_memory.db, выбирает 0–3 релевантных напоминания и инжектирует их в системный промпт исполнителя. При сбое — graceful degradation (пустой контекст), ошибка логируется. | draft |
| DP.SC.162 | External Session Request | Пилот ведёт полноценную multi-turn рабочую сессию через Telegram — эквивалент окна VS Code, но асинхронно. Поддерживаются: диалог вопрос→ответ→вопрос, работа по РП, операции с календарём, создание РП, поиск по IWE. Все действия трекаются. | draft |
| DP.SC.163 | Серверные агенты через Gateway (MVP) | Пользователь через Gateway получает результат работы агента (Стратег, Экстрактор) в виде коммита в свой GitHub-репозиторий — без локального CLI, с тем же артефактом, что и через VS Code | draft |
| DP.SC.164 | Доставка персонального руководства пилоту | Ежедневный daily и еженедельный weekly файл персонального руководства, отражающий контекст пилота (активные РП, captures, посты, рефлексии, cp-профиль), доставляется в его репо `personal-guide/<пилот>/` по расписанию; не зависит от ритуалов ОРЗ. | draft |
| DP.SC.165 | Scope-control для bridge write-tools | Bridge write-tools (`personal_write`, `personal_propose_capture`) проходят server-side scope check в gateway-mcp; bridge cache TTL=60s даёт быстрый deny без round-trip | draft |
| DP.SC.166 | Сторож новых задач — ежедневный дайджест в Telegram | Раз в сутки (до 09:00) обойти все github-репо в ~/IWE/*, найти задачи, созданные за последние 2 дня и ещё не показанные пилоту, классифицировать важность и отправить дайджест в Telegram. Критичные (потеря данных / безопасность / регрессия) — отдельной пометкой. Дедуп через state-файл, идемпотентно. | draft |
| DP.SC.167 | hermes-chat | — | draft |
| DP.SC.168 | Онбординг IWE — зонтичное обещание | — | draft |
| DP.SC.169 | conductor-lite | — | deprecated |
| DP.SC.170 | onboarder | — | draft |
| DP.SC.171 | conveyor-development | — | draft |
| DP.SC.172 | База инженерного стиля кода | Агент-разработчик выдаёт код craft-уровня (без перечисленных запахов) при написании кода в репозиториях IWE | active |
| DP.SC.173 | Реестр языковых стилей | Реестр хранит языковые стили как данные (оси, голос, тон, регистры, жанры, пресеты) и компилирует фрагмент системного промпта по полному композитному ключу детерминированно | draft |
| DP.SC.174 | Диспетчер контекста стилей | Диспетчер вычисляет полный композитный ключ из сырого контекста хода (детектор канала + роль читателя), запрашивает фрагмент у реестра и инъектирует его в промпт до первого токена | draft |
| DP.SC.175 | Выбор стиля пользователем | Пользователь настраивает стиль по осям (модель Grammarly), пресетам или из текста-примера; выбор пишется как user_override_hash в каскад платформа→канал→пользователь и применяется со следующего хода | draft |

### SYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SYS.001 | Детерминированные системы | Реестр детерминированных подсистем. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |

### VM

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.VM.001 | P1 P9 Calibration Matrix | Девять промежуточных польз новичка: как система засекает достижение каждой (прокси/БД) и как Онбордер ведёт к ней (доставка/предусловие/характеристика Первокурсника/событие тира). | — |

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
- Missing `summary`: DP.D.099 (DP.D.099-read-metric-vs-downstream-effect.md)
- Missing `summary`: DP.D.102 (DP.D.102-event-calendar-four-channels.md)
- Missing `summary`: DP.D.104 (DP.D.104-progress-to-reward-vs-balance.md)
- Missing `summary`: DP.D.108 (DP.D.108-behavioral-vs-technical-bottleneck.md)
- Missing `summary`: DP.D.109 (DP.D.109-toc-bottleneck-vs-readiness-gap.md)
- Missing `summary`: DP.D.110 (DP.D.110-pillar-text-vs-conversion-post.md)
- Missing `summary`: DP.D.111 (DP.D.111-triaging-vs-execution.md)
- Missing `summary`: DP.D.112 (DP.D.112-cutover-infra-vs-marketing-launch.md)
- Missing `summary`: DP.D.121 (DP.D.121-toc-system-vs-portfolio.md)
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
- Missing `summary`: DP.D.079 (DP.D.079-smoke-technical-vs-processing-signal.md)
- Missing `summary`: DP.D.080 (DP.D.080-control-vs-operation.md)
- Missing `summary`: DP.D.089 (DP.D.089-cascading-vs-independent-failure.md)
- Missing `summary`: DP.D.090 (DP.D.090-structural-smoke-vs-e2e-smoke.md)
- Missing `summary`: DP.D.091 (DP.D.091-aligned-boundary-vs-tandem-scales.md)
- Missing `summary`: DP.D.092 (DP.D.092-rate-limit-vs-value.md)
- Missing `summary`: DP.D.093 (DP.D.093-classifier-label-vs-source.md)
- Missing `summary`: DP.D.094 (DP.D.094-temporal-correlation-vs-causation.md)
- Missing `summary`: DP.D.095 (DP.D.095-iwe-vs-platform-boundary.md)
- Missing `summary`: DP.D.096 (DP.D.096-parliament-model-agent-memory.md)
- Missing `summary`: DP.D.097 (DP.D.097-loop-control-at-caller-not-callee.md)
- Missing `summary`: DP.D.098 (DP.D.098-ground-truth-vs-self-assessment.md)
- Missing `summary`: DP.D.101 (DP.D.101-shared-module-sharing-symlink-submodule-vendor.md)
- Missing `summary`: DP.D.114 (DP.D.114-software-factory-vs-platform.md)
- Missing `summary`: DP.D.115 (DP.D.115-distributed-vs-monolithic-orchestration.md)
- Missing `summary`: DP.D.116 (DP.D.116-semantic-compiler-vs-ssg.md)
- Missing `summary`: DP.D.117 (DP.D.117-render-pipelines-vs-products-vs-regions.md)
- Missing `summary`: DP.D.118 (DP.D.118-roles-n-dimensional-orthogonality.md)
- Missing `summary`: DP.D.119 (DP.D.119-domain-role-vs-turn-role.md)
- Missing `summary`: DP.D.122 (DP.D.122-continuous-trend-vs-point-in-time.md)
- Missing `summary`: DP.D.123 (DP.D.123-state-dependency-test-for-skill-classification.md)
- Missing `summary`: DP.D.124 (DP.D.124-agent-persona-vs-runtime.md)
- Missing `summary`: DP.D.125 (DP.D.125-two-orthogonal-axes-vs-matrix.md)
- Missing `summary`: DP.D.126 (DP.D.126-interface-vs-tier.md)
- Missing `summary`: DP.D.127 (DP.D.127-aux-class-vs-narrative.md)
- Missing `summary`: DP.D.128 (DP.D.128-static-prompt-vs-interactive-channel.md)
- Missing `summary`: DP.D.129 (DP.D.129-historical-membership-vs-current-channel.md)
- Missing `summary`: DP.D.130 (DP.D.130-two-axis-onboarding-model.md)
- Missing `summary`: DP.D.131 (DP.D.131-costume-vs-equipment.md)
- Missing `summary`: DP.D.132 (DP.D.132-firstokursnik-vs-member.md)
- Missing `summary`: DP.IWE.003 (DP.IWE.003-gateway-architecture.md)
- Missing `summary`: DP.IWE.004 (DP.IWE.004-iwe-interfaces.md)
- Missing `summary`: DP.IWE.005 (DP.IWE.005-local-gateway.md)
- Missing `summary`: DP.IWE.006 (DP.IWE.006-personal-guide-channels.md)
- Missing `summary`: DP.IWE.008 (DP.IWE.008-byob-principle.md)
- Missing `summary`: DP.IWE.009 (DP.IWE.009-iwe-perimeter.md)
- Missing `summary`: DP.IWE.010 (DP.IWE.010-iwe-machine.md)
- Missing `summary`: DP.IWE.011-adapter-claude-code (claude-code-adapter.md)
- Missing `summary`: DP.IWE.011-adapter-headless (headless-adapter.md)
- Missing `summary`: DP.IWE.011 (DP.IWE.011-runtime-host-contract.md)
- Missing `summary`: DP.ROLE.053 (DP.ROLE.053-decomposer.md)
- Missing `summary`: DP.ROLE.065 (DP.ROLE.065-hermes-proxy-tool.md)
- Missing `summary`: DP.ROLE.067 (DP.ROLE.067-onboarder.md)
- Missing `summary`: DP.ROLE.073 (DP.ROLE.073-style-registry-keeper.md)
- Missing `summary`: DP.ROLE.074 (DP.ROLE.074-style-dispatcher.md)
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
- Missing `summary`: DP.M.086 (DP.M.086-notification-log-cheap-idempotency.md)
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
- Missing `summary`: DP.M.109 (DP.M.109-connection-vs-foundation-phrasing.md)
- Missing `summary`: DP.M.110 (DP.M.110-declarative-nudge-markers.md)
- Missing `summary`: DP.M.111 (DP.M.111-majority-vote-structure-drift-detector.md)
- Missing `summary`: DP.M.112 (DP.M.112-run-skill-headless-dispatch.md)
- Missing `summary`: DP.M.113 (DP.M.113-earned-total-vs-points-separation.md)
- Missing `summary`: DP.M.114 (DP.M.114-historical-bonus-cap.md)
- Missing `summary`: DP.M.117 (DP.M.117-cohort-content-as-declarative-json.md)
- Missing `summary`: DP.M.118 (DP.M.118-cohort-intake-survey-freeze.md)
- Missing `summary`: DP.M.120 (DP.M.120-boundary-mapping-constant.md)
- Missing `summary`: DP.M.121 (DP.M.121-universal-guide-phases-f0-f6.md)
- Missing `summary`: DP.M.122 (DP.M.122-security-culture-pilot.md)
- Missing `summary`: DP.M.123 (DP.M.123-backup-as-method.md)
- Missing `summary`: DP.M.124 (DP.M.124-encryption-as-method.md)
- Missing `summary`: DP.M.137 (DP.M.137-auto-trigger-subagent-review-first-subsection.md)
- Missing `summary`: DP.M.138 (DP.M.138-dispatcher-origin-sync-after-headless-agent.md)
- Missing `summary`: DP.M.139 (DP.M.139-lint-placeholder-as-ontology-gap-detector.md)
- Missing `summary`: DP.M.140 (DP.M.140-forming-to-formalized-ontology-term-lifecycle.md)
- Missing `summary`: DP.M.141 (DP.M.141-pack-refs-source-docs-ontology-anchor.md)
- Missing `summary`: DP.M.142 (DP.M.142-ci-setup-flag-mode-separation.md)
- Missing `summary`: DP.M.145 (DP.M.145-terminology-replace-multi-pass-verify.md)
- Missing `summary`: DP.M.146 (DP.M.146-working-hypothesis-marker.md)
- Missing `summary`: DP.M.147 (DP.M.147-semantic-first-performance-later.md)
- Missing `summary`: DP.M.148 (DP.M.148-audit-cascade-related-documents.md)
- Missing `summary`: DP.M.149 (DP.M.149-bearer-shared-secret-compat-auth-mode.md)
- Missing `summary`: DP.M.150 (DP.M.150-multi-driver-compat-duck-typing.md)
- Missing `summary`: DP.M.156 (DP.M.156-upgrade-markers-in-service-contract.md)
- Missing `summary`: DP.M.157 (DP.M.157-manifest-coverage-ci-check.md)
- Missing `summary`: DP.M.158 (DP.M.158-archgate-defer-pattern.md)
- Missing `summary`: DP.M.159 (DP.M.159-skill-as-single-entry-point.md)
- Missing `summary`: DP.M.160 (DP.M.160-single-point-degradation-tracking.md)
- Missing `summary`: DP.M.161 (DP.M.161-pack-maturity-estimation-parameter.md)
- Missing `summary`: DP.M.162 (DP.M.162-peer-adversarial-critique-methodology-guides.md)
- Missing `summary`: DP.M.163 (DP.M.163-deferred-phase-finalization-checkpoint.md)
- Missing `summary`: DP.M.165 (DP.M.165-soft-streak-reset.md)
- Missing `summary`: DP.M.166 (DP.M.166-referral-credit-not-points.md)
- Missing `summary`: DP.M.167 (DP.M.167-refinement-prompt-by-previous-length.md)
- Missing `summary`: DP.M.168 (DP.M.168-post-deploy-regression-first-hypothesis.md)
- Missing `summary`: DP.M.169 (DP.M.169-experimental-weight-guard-condition.md)
- Missing `summary`: DP.M.170 (DP.M.170-router-role-dispatch-separation.md)
- Missing `summary`: DP.M.171 (DP.M.171-fpf-sync-delta-map.md)
- Missing `summary`: DP.M.172 (DP.M.172-knowledge-file-archive-vs-delete.md)
- Missing `summary`: DP.M.173 (DP.M.173-artifact-first-contract-with-confidence.md)
- Missing `summary`: DP.M.174 (DP.M.174-triple-hash-idempotency-llm-pipeline.md)
- Missing `summary`: DP.M.176 (DP.M.176-wp-inbox-flat-vs-folder.md)
- Missing `summary`: DP.M.178 (DP.M.178-wp-triage-three-step-filter.md)
- Missing `summary`: DP.M.179 (DP.M.179-single-source-dashboard-script.md)
- Missing `summary`: DP.M.180 (DP.M.180-defer-policy-no-auto-escalate.md)
- Missing `summary`: DP.M.181 (DP.M.181-multi-turn-session-thread-pattern.md)
- Missing `summary`: DP.M.182 (DP.M.182-dual-sla-acknowledgment-completion.md)
- Missing `summary`: DP.M.183 (DP.M.183-level-dependent-bonus-caps-ema.md)
- Missing `summary`: DP.M.184 (DP.M.184-ema-bonus-exchange-rate-smoothing.md)
- Missing `summary`: DP.M.185 (DP.M.185-power-law-effort-scoring.md)
- Missing `summary`: DP.M.186 (DP.M.186-15-second-onboarding-promise-test.md)
- Missing `summary`: DP.M.187 (DP.M.187-newcomer-fixed-multiplier-window.md)
- Missing `summary`: DP.M.188 (DP.M.188-backend-stages-to-ui-grades-mapping.md)
- Missing `summary`: DP.M.189 (DP.M.189-promise-floor-for-self-balancing-variable.md)
- Missing `summary`: DP.M.190 (DP.M.190-live-demo-three-level-fallback.md)
- Missing `summary`: DP.M.191 (DP.M.191-funnel-cta-temporal-proximity.md)
- Missing `summary`: DP.M.192 (DP.M.192-c9-concrete-scene-replacement.md)
- Missing `summary`: DP.M.193 (DP.M.193-hybrid-fix-regex-tolerance-local-unification.md)
- Missing `summary`: DP.M.194 (DP.M.194-anchored-regex-frontmatter-aware.md)
- Missing `summary`: DP.M.195 (DP.M.195-pull-driven-feature-activation.md)
- Missing `summary`: DP.M.196 (DP.M.196-upsert-runtime-verify-double-delta.md)
- Missing `summary`: DP.M.197 (DP.M.197-fix-contract-spec-with-regression-checks.md)
- Missing `summary`: DP.M.198 (DP.M.198-atomic-state-change-with-user-reply.md)
- Missing `summary`: DP.M.199 (DP.M.199-three-tier-config-parameters.md)
- Missing `summary`: DP.M.200 (DP.M.200-self-financing-referral-mechanism.md)
- Missing `summary`: DP.M.201 (DP.M.201-separate-api-keys-per-workload.md)
- Missing `summary`: DP.M.202 (DP.M.202-loyalty-community-events-dual-cap.md)
- Missing `summary`: DP.M.203 (DP.M.203-neon-multi-db-fdw-schema-prefix.md)
- Missing `summary`: DP.M.205 (DP.M.205-gamification-event-controllability-rate-limit.md)
- Missing `summary`: DP.M.206 (DP.M.206-fast-fail-and-restart-over-reconnect.md)
- Missing `summary`: DP.M.207 (DP.M.207-explicit-choice-before-stateful-default.md)
- Missing `summary`: DP.M.208 (DP.M.208-diagnostics-before-behavioral-nudge.md)
- Missing `summary`: DP.M.209 (DP.M.209-dry-run-half-of-production-migration.md)
- Missing `summary`: DP.M.210 (DP.M.210-three-tier-stuck-user-segmentation.md)
- Missing `summary`: DP.M.211 (DP.M.211-concept-coverage-introduces-registration-gap.md)
- Missing `summary`: DP.M.212 (DP.M.212-discourse-webhook-iwe-event-pipeline-mapping.md)
- Missing `summary`: DP.M.213 (DP.M.213-upsert-xmax-insert-detect.md)
- Missing `summary`: DP.M.214 (DP.M.214-silent-oauth-token-provisioning.md)
- Missing `summary`: DP.M.215 (DP.M.215-sql-not-exists-guard-for-predicate-based-row-exclusion.md)
- Missing `summary`: DP.M.217 (DP.M.217-glue-requires-executor-pipeline-decomposition.md)
- Missing `summary`: DP.M.218 (DP.M.218-close-check-open-autofix-defense-in-depth.md)
- Missing `summary`: DP.M.219 (DP.M.219-by-script-marker-idempotent-injection.md)
- Missing `summary`: DP.M.220 (DP.M.220-threshold-or-time-auto-commit.md)
- Missing `summary`: DP.M.223 (DP.M.223-marp-dark-theme-layout-classes.md)
- Missing `summary`: DP.M.225 (DP.M.225-identity-anchor-character-seminar.md)
- Missing `summary`: DP.M.226 (DP.M.226-progressive-card-filling-seminar.md)
- Missing `summary`: DP.M.230 (DP.M.230-dual-level-wait-for-infinite-retry-guard.md)
- Missing `summary`: DP.M.231 (DP.M.231-simultaneous-domain-recovery-as-main-loop-block-marker.md)
- Missing `summary`: DP.M.232 (DP.M.232-umbrella-decomposition-domain-specific-vs-infra.md)
- Missing `summary`: DP.M.233 (DP.M.233-cutover-date-vs-backfill.md)
- Missing `summary`: DP.M.234 (DP.M.234-two-condition-open-state-detector.md)
- Missing `summary`: DP.M.235 (DP.M.235-umbrella-wp-rescope-audit.md)
- Missing `summary`: DP.M.236 (DP.M.236-phase-split-by-verification-class.md)
- Missing `summary`: DP.M.237 (DP.M.237-auto-route-plus-manual-override-affordance.md)
- Missing `summary`: DP.M.238 (DP.M.238-pre-articulated-open-questions-deferred-phase.md)
- Missing `summary`: DP.M.239 (DP.M.239-defense-in-depth-bail-out-regex-refactor.md)
- Missing `summary`: DP.M.240 (DP.M.240-self-recoverable-tooling-symlink-writable-path.md)
- Missing `summary`: DP.M.241 (DP.M.241-personal-guide-render.md)
- Missing `summary`: DP.M.242 (DP.M.242-ar5-pack-quality-baseline.md)
- Missing `summary`: DP.M.243 (DP.M.243-discriminator-column-sti-pattern.md)
- Missing `summary`: DP.M.244 (DP.M.244-trust-boundary-server-side-authz.md)
- Missing `summary`: DP.M.245 (DP.M.245-cp-profile-adaptive-facilitation.md)
- Missing `summary`: DP.M.246 (DP.M.246-content-debt-triage-inbox.md)
- Missing `summary`: DP.M.247 (DP.M.247-pre-llm-eligibility-gate.md)
- Missing `summary`: DP.M.248 (DP.M.248-composable-cli-linter-per-rule.md)
- Missing `summary`: DP.M.249 (DP.M.249-delivery-tracker-umbrella-wp.md)
- Missing `summary`: DP.M.250 (DP.M.250-glossary-driven-lint-yaml.md)
- Missing `summary`: DP.M.251 (DP.M.251-nighttime-rollout-with-rollback-verifier.md)
- Missing `summary`: DP.M.252 (DP.M.252-satisfied-by-existing-content-pre-build-scout.md)
- Missing `summary`: DP.M.253 (DP.M.253-seminar-orientation-map-max-impact-triple.md)
- Missing `summary`: DP.M.254 (DP.M.254-container-abstraction-mapping.md)
- Missing `summary`: DP.M.255 (DP.M.255-poly-root-context-assembly.md)
- Missing `summary`: DP.M.256 (DP.M.256-pointer-only-fork-closure.md)
- Missing `summary`: DP.M.257 (DP.M.257-closed-partial-multi-channel-resumption.md)
- Missing `summary`: DP.M.258 (DP.M.258-cross-component-trigger-body-search-path.md)
- Missing `summary`: DP.M.259 (DP.M.259-resource-constraint-dominates-portfolio.md)
- Missing `summary`: DP.M.260 (DP.M.260-intentional-disablement-third-hypothesis.md)
- Missing `summary`: DP.M.261 (DP.M.261-port-working-sql-from-known-good.md)
- Missing `summary`: DP.M.262 (DP.M.262-bidirectional-cross-reference-exec-coupling.md)
- Missing `summary`: DP.M.263 (DP.M.263-pack-coupling-cascade.md)
- Missing `summary`: DP.M.264 (DP.M.264-threshold-audit-scenario.md)
- Missing `summary`: DP.M.265 (DP.M.265-delta-signal-not-raw-values.md)
- Missing `summary`: DP.M.266 (DP.M.266-internal-service-auth-shared-secret-and-user-id-header.md)
- Missing `summary`: DP.M.267 (DP.M.267-grep-marker-deferred-auto-registry.md)
- Missing `summary`: DP.M.268 (DP.M.268-auto-generated-ownership-marker.md)
- Missing `summary`: DP.M.269 (DP.M.269-bidirectional-registry-drift-guard.md)
- Missing `summary`: DP.M.270 (DP.M.270-resolve-instructions-level.md)
- Missing `summary`: DP.M.271 (DP.M.271-lazy-channel-aware-resource-creation.md)
- Missing `summary`: DP.M.272 (DP.M.272-role-unpacking-via-split-to.md)
- Missing `summary`: DP.M.273 (DP.M.273-explicit-prefix-guard-disambiguation.md)
- Missing `summary`: DP.M.274 (DP.M.274-ironman-three-mastery-levels.md)
- Missing `summary`: DP.M.275 (DP.M.275-sc-decomposition-via-umbrella.md)
- Missing `summary`: DP.M.276 (DP.M.276-add-not-rename-on-unpacking.md)
- Missing `summary`: DP.M.277 (DP.M.277-single-source-method-n-surfaces.md)
- Missing `summary`: DP.M.278 (DP.M.278-hybrid-corpus-audit-protocol.md)
- Missing `summary`: DP.M.279 (DP.M.279-held-patch-pattern.md)
- Missing `summary`: DP.M.280 (DP.M.280-allow-fallback-cutover-pattern.md)
- Missing `summary`: DP.M.281 (DP.M.281-recurring-error-diagnosis.md)
- Missing `summary`: DP.M.282 (DP.M.282-function-first-onboarding.md)
- Missing `summary`: DP.M.283 (DP.M.283-byok-first-tier-unlock.md)
- Missing `summary`: DP.M.284 (DP.M.284-inline-cat-over-add-dir-cli.md)
- Missing `summary`: DP.M.285 (DP.M.285-dual-write-safety-net-projection-migration.md)
- Missing `summary`: DP.M.286 (DP.M.286-cold-review-frontmatter-anchors-pass.md)
- Missing `summary`: DP.M.287 (DP.M.287-grace-window-overlapping-scheduled-jobs.md)
- Missing `summary`: DP.M.288 (DP.M.288-dual-nudge-same-day-reengagement.md)
- Missing `summary`: DP.M.290 (DP.M.290-explicit-next-step-numbering.md)
- Missing `summary`: DP.M.291 (DP.M.291-patch-object-vs-string-path-mock.md)
- Missing `summary`: DP.M.292 (DP.M.292-tier-source-provenance.md)
- Missing `summary`: DP.M.293 (DP.M.293-graceful-degradation-secondary-db-timeout.md)
- Missing `summary`: DP.M.294 (DP.M.294-extraction-report-lifecycle-applied-archive.md)
- Missing `summary`: DP.M.295 (DP.M.295-digital-twin-derived-over-primitive.md)
- Missing `summary`: DP.M.296 (DP.M.296-diagnosis-drill-down-all-weak-slices.md)
- Missing `summary`: DP.M.297 (DP.M.297-platform-specific-path-from-params-yaml.md)
- Missing `summary`: DP.M.298 (DP.M.298-fail-closed-scope-sidecar.md)
- Missing `summary`: DP.M.299 (DP.M.299-rotation-impact-map.md)
- Missing `summary`: DP.METHOD.051 (DP.METHOD.051-n8n-builtin-healthz.md)
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
- Missing `summary`: DP.FM.045 (DP.FM.045-log-before-send-blocks-retry.md)
- Missing `summary`: DP.FM.047 (DP.FM.047-third-party-pii-vendor-gate.md)
- Missing `summary`: DP.FM.048 (DP.FM.048-cf-bot-fight-mode-xhr-block.md)
- Missing `summary`: DP.FM.049 (DP.FM.049-document-centric-bottleneck.md)
- Missing `summary`: DP.FM.050 (DP.FM.050-markdown-bold-regex-punctuation.md)
- Missing `summary`: DP.FM.051 (DP.FM.051-on-conflict-nullable-unique-incompleteness.md)
- Missing `summary`: DP.FM.054 (DP.FM.054-lint-green-no-body-structure-check.md)
- Missing `summary`: DP.FM.055 (DP.FM.055-deprecated-not-removed-from-runner.md)
- Missing `summary`: DP.FM.056 (DP.FM.056-deprecated-not-deleted-runner-out-of-sync.md)
- Missing `summary`: DP.FM.058 (DP.FM.058-pilot-default-open-pii-accumulation.md)
- Missing `summary`: DP.FM.059 (DP.FM.059-hook-command-relative-path.md)
- Missing `summary`: DP.FM.060 (DP.FM.060-half-migration-manifest-runner-split.md)
- Missing `summary`: DP.FM.061 (DP.FM.061-ci-optional-secret-hard-fail.md)
- Missing `summary`: DP.FM.070 (DP.FM.070-dispatcher-git-reset-race-condition.md)
- Missing `summary`: DP.FM.072 (DP.FM.072-canonical-form-introduces-pack-refs.md)
- Missing `summary`: DP.FM.073 (DP.FM.073-protocol-coverage-gap-mentioned-not-enforced.md)
- Missing `summary`: DP.FM.074 (DP.FM.074-sm-callback-router-missing.md)
- Missing `summary`: DP.FM.078 (DP.FM.078-ghost-canonical-pointer.md)
- Missing `summary`: DP.FM.079 (DP.FM.079-impact-group-as-multiplier.md)
- Missing `summary`: DP.FM.080 (DP.FM.080-symptom-masks-multiple-defects.md)
- Missing `summary`: DP.FM.081 (DP.FM.081-probe-double-count-degradation.md)
- Missing `summary`: DP.FM.082 (DP.FM.082-president-architecture-disguised-as-parliament.md)
- Missing `summary`: DP.FM.083 (DP.FM.083-empty-field-url-injection.md)
- Missing `summary`: DP.FM.084 (DP.FM.084-oauth-cdn-redirect-uri-no-preflight.md)
- Missing `summary`: DP.FM.085 (DP.FM.085-hook-installer-anti-patterns.md)
- Missing `summary`: DP.FM.086 (DP.FM.086-dangling-intent-pending-no-due-date.md)
- Missing `summary`: DP.FM.087 (DP.FM.087-watchdog-new-script-overdue-false-positive.md)
- Missing `summary`: DP.FM.088 (DP.FM.088-done-phase-open-checkboxes-hidden-debt.md)
- Missing `summary`: DP.FM.089 (DP.FM.089-test-blast-radius-shared-flow-io.md)
- Missing `summary`: DP.FM.090 (DP.FM.090-ordinal-guard-vs-semantic-role-in-turn-dispatcher.md)
- Missing `summary`: DP.FM.091 (DP.FM.091-god-table-cross-domain-coupling.md)
- Missing `summary`: DP.FM.092 (DP.FM.092-fire-and-forget-temporal-coupling.md)
- Missing `summary`: DP.FM.093 (DP.FM.093-retry-storm-guard-silent-orphan.md)
- Missing `summary`: DP.FM.094 (DP.FM.094-binary-counter-masks-legitimate-non-advance.md)
- Missing `summary`: DP.FM.095 (DP.FM.095-feature-flag-without-alter-function.md)
- Missing `summary`: DP.FM.096 (DP.FM.096-config-without-emitter-invisible-bug.md)
- Missing `summary`: DP.FM.097 (DP.FM.097-deployment-path-drift-home-vs-repo.md)
- Missing `summary`: DP.FM.098 (DP.FM.098-sm-mutex-guard-queue-flow-bypass.md)
- Missing `summary`: DP.FM.099 (DP.FM.099-notify-subscription-tied-to-connection.md)
- Missing `summary`: DP.FM.100 (DP.FM.100-stale-snapshot-silent-misdiagnosis.md)
- Missing `summary`: DP.FM.101 (DP.FM.101-rule-engine-noop-missing-rule-silent-drop.md)
- Missing `summary`: DP.FM.102 (DP.FM.102-boolean-flag-hardcoded-constant-silent-underpayment.md)
- Missing `summary`: DP.FM.103 (DP.FM.103-coverage-script-no-guide-scope-filter-false-fail.md)
- Missing `summary`: DP.FM.104 (DP.FM.104-missing-reverse-identity-lookup.md)
- Missing `summary`: DP.FM.105 (DP.FM.105-internal-probe-blind-to-own-failure.md)
- Missing `summary`: DP.FM.106 (DP.FM.106-anthropic-api-usage-limit-terminal-failure.md)
- Missing `summary`: DP.FM.107 (DP.FM.107-volatile-function-upsert-trigger-cascade.md)
- Missing `summary`: DP.FM.108 (DP.FM.108-owner-empty-default-from-single-source.md)
- Missing `summary`: DP.FM.109 (DP.FM.109-sentinel-empty-string-past-scheduler.md)
- Missing `summary`: DP.FM.110 (DP.FM.110-unix-socket-no-protocol-handshake.md)
- Missing `summary`: DP.FM.111 (DP.FM.111-rule-engine-dormant-low-trust.md)
- Missing `summary`: DP.FM.113 (DP.FM.113-regex-search-swallows-second-violation.md)
- Missing `summary`: DP.FM.114 (DP.FM.114-adapter-dependency-silent-regression.md)
- Missing `summary`: DP.FM.115 (DP.FM.115-peer-agent-overwrite-without-read.md)
- Missing `summary`: DP.FM.116 (DP.FM.116-external-id-path-traversal.md)
- Missing `summary`: DP.FM.117 (DP.FM.117-double-count-compound-formula-component.md)
- Missing `summary`: DP.FM.118 (DP.FM.118-ambiguous-metric-name-theoretical-vs-operational.md)
- Missing `summary`: DP.FM.119 (DP.FM.119-concurrent-writers-break-threshold-logic.md)
- Missing `summary`: DP.FM.120 (DP.FM.120-zero-masking-instead-of-rootfix.md)
- Missing `summary`: DP.FM.121 (DP.FM.121-dry-run-side-effect.md)
- Missing `summary`: DP.FM.122 (DP.FM.122-spec-without-impl.md)
- Missing `summary`: DP.FM.123 (DP.FM.123-reverse-proxy-truncates-long-running-handler.md)
- Missing `summary`: DP.FM.124 (DP.FM.124-lru-cache-for-async-resource-with-lifecycle.md)
- Missing `summary`: DP.FM.125 (DP.FM.125-short-name-fallback-authorization-bypass.md)
- Missing `summary`: DP.FM.126 (DP.FM.126-polymorphic-return-breaks-shared-helper-callsites.md)
- Missing `summary`: DP.FM.127 (DP.FM.127-python39-future-annotations-compat.md)
- Missing `summary`: DP.FM.128 (DP.FM.128-pytest-collection-error-missing-attribute.md)
- Missing `summary`: DP.FM.129 (DP.FM.129-broken-symlink-silent-config-empty.md)
- Missing `summary`: DP.FM.130 (DP.FM.130-os-expanduser-no-shell-vars.md)
- Missing `summary`: DP.FM.132 (DP.FM.132-microservice-tier-sot-mismatch.md)
- Missing `summary`: DP.FM.133 (DP.FM.133-backup-restore-no-3way-merge.md)
- Missing `summary`: DP.FM.134 (DP.FM.134-vocabulary-split-aux-subsections.md)
- Missing `summary`: DP.FM.135 (DP.FM.135-projection-rule-no-backfill-fallback-mask.md)
- Missing `summary`: DP.FM.137 (DP.FM.137-asymmetric-alert-suppression-paths.md)
- Missing `summary`: DP.FM.138 (DP.FM.138-shared-db-without-env-discriminator.md)
- Missing `summary`: DP.FM.139 (DP.FM.139-llm-proxy-default-timeout-too-short.md)
- Missing `summary`: DP.FM.140 (DP.FM.140-cutover-incomplete-side-channel.md)
- Missing `summary`: DP.FM.141 (DP.FM.141-shared-queue-no-tenant-key.md)
- Missing `summary`: DP.FM.142 (DP.FM.142-new-codepath-no-retry-symmetry.md)
- Missing `summary`: DP.FM.143 (DP.FM.143-ppid-fallback-stale-pidfile-multiagent.md)
- Missing `summary`: DP.FM.144 (DP.FM.144-side-effect-check-blocks-primary-flow.md)
- Missing `summary`: DP.FM.145 (DP.FM.145-fdw-read-only-cross-db-write.md)
- Missing `summary`: DP.FM.146 (DP.FM.146-unconditional-helper-always-fires-gate.md)
- Missing `summary`: DP.SOTA.029 (DP.SOTA.029-ai-era-two-crisis-groups.md)
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
- Missing `summary`: DP.SC.167 (DP.SC.167-hermes-chat.md)
- Missing `summary`: DP.SC.168 (DP.SC.168-onboarding.md)
- Missing `summary`: DP.SC.169 (DP.SC.169-conductor-lite.md)
- Missing `summary`: DP.SC.170 (DP.SC.170-onboarder.md)
- Missing `summary`: DP.SC.171 (DP.SC.171-conveyor-development.md)

---

*Generated by `scripts/generate-map.py` on 2026-06-11*