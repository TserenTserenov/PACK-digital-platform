---
id: DP.MAP.001
name: Pack Navigation Map
scope: full-pack
created: 2026-07-23
last_updated: 2026-07-23
generated: true
---

# [DP.MAP.001] Pack Navigation Map

> Auto-generated from frontmatter on 2026-07-23. Do not edit manually.

---

## Statistics

| Kind | Count |
|------|-------|
| AISYS (AISYS) | 4 |
| ARCH (ARCH) | 9 |
| ASSIST (ASSIST) | 1 |
| CONCEPT (CONCEPT) | 4 |
| Distinctions (D) | 266 |
| ECON (ECON) | 1 |
| EXOCORTEX (EXOCORTEX) | 1 |
| Failure Modes (FM) | 287 |
| IWE (IWE) | 13 |
| KR (KR) | 3 |
| Methods (M) | 366 |
| Maps (MAP) | 2 |
| METHOD (METHOD) | 132 |
| NAV (NAV) | 1 |
| ONT (ONT) | 1 |
| ORG (ORG) | 1 |
| ROADMAP (ROADMAP) | 2 |
| ROLE (ROLE) | 76 |
| RUNBOOK (RUNBOOK) | 1 |
| SC (SC) | 158 |
| SoTA Annotations (SOTA) | 34 |
| SYS (SYS) | 1 |
| VM (VM) | 1 |
| Work Products (WP) | 16 |
| **Total** | **1381** |

## Distinctions

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.D.001 | Объект ≠ Модель | — | active |
| DP.D.002 | Модель ≠ Данные | — | active |
| DP.D.003 | Данные ≠ Представление (view) | — | active |
| DP.D.004 | Индикатор ≠ Факт | — | active |
| DP.D.005 | Входной индикатор ≠ Производный индикатор | — | active |
| DP.D.006 | Генеративный текст ≠ Source-of-truth | — | active |
| DP.D.007 | Качество модели ≠ Качество данных | — | active |
| DP.D.008 | Синхронизация ≠ Копирование | — | active |
| DP.D.009 | ИИ-агент ≠ ИИ-ассистент (режимы одной системы, не разные типы) | — | active |
| DP.D.010 | Архитектурная характеристика ≠ Архитектурный принцип | — | active |
| DP.D.011 | Platform-space ≠ User-space | — | active |
| DP.D.012 | Знание ≠ Информация | — | active |
| DP.D.013 | Навигация ≠ Поиск | — | active |
| DP.D.014 | Оперативная память ≠ Справочник | — | active |
| DP.D.015 | ИИ-система ≠ ИТ-система | — | active |
| DP.D.016 | Capture-to-Pack ≠ Knowledge Extraction | — | active |
| DP.D.017 | Стратегический DDD ≠ Тактический DDD | — | active |
| DP.D.018 | Model ≠ View (3 паттерна) | — | active |
| DP.D.019 | DSL ≠ DSLM | — | active |
| DP.D.020 | Capture During-Work ≠ After-Work | — | active |
| DP.D.021 | Вайб-моделирование ≠ Профессиональное моделирование | — | active |
| DP.D.022 | Три уровня проверяемости (формальная ≠ семантическая ≠ операционная) | — | active |
| DP.D.023 | Роль ≠ Агент; Агент ≠ Инструмент | — | active |
| DP.D.024 | Semantic Search ≠ Keyword Search | — | active |
| DP.D.025 | Harness ≠ Agent | Harness (упряжь/обвязка) определяет результат больше, чем мощность агента/модели | active |
| DP.D.026 | Хранимый счётчик ≠ Вычисляемая проекция | — | active |
| DP.D.027 | Content Budget Model (3 оси) | Длина, глубина и персонализация контента — три независимые оси, управляемые раздельно | active |
| DP.D.028 | User Data Tiers — тирование данных пользователя | Данные пользователя растут с тиром платформы: T0 без Ory (telegram_id) → T1 с Ory (UUID) → T2 (активная подписка) профиль + история + универсальные руководства (одинаковые для всех) → T3 (подключён любой AI-клиент: claude.ai / Claude Code / VS Code / Telegram) персональные артефакты — персональное руководство (WP-149) + Гермес знает историю (Память.Derived) → T4 (+ GitHub) личный Pack + ИИ-агенты (со-мыслитель). T3-условие = AI-клиент подключён, НЕ «ЦД заполнен» (устаревшее, см. DP.D.052; семантика T3 — консенсус WP-406 Ф13). Ортогональные оси: TM (наставник), TA (администратор), TD (разработчик) | active |
| DP.D.029 | Language Model ≠ World Model | LLM = пассивные знания о мире из текстов (кабинетный учёный). World Model = активная модель, обновляемая из взаимодействия с реальностью (инженер). Критерий: замыкает ли система цикл действие-измерение-обновление | active |
| DP.D.030 | Топология деплоя платформы | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.031 | MCP Access Model: публичный vs приватный | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.032 | Единый Circuit Breaker для внешних зависимостей | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.033 | Role-Centric Architecture (Ролецентричная архитектура) | Роль описывается независимо от исполнителя. Исполнитель выбирается и подготавливается отдельно. Роль = маска, которую надевает система (сама — если агент, или по воле другого агента — если инструмент). Одно имя (например, 'Синхронизатор') может обозначать и роль, и систему-исполнителя — это разные ракурсы, не тождество. | active |
| DP.D.034 | Three-Axis Access Control Model (Трёхосевая модель доступов) | Доступ на платформе определяется тремя ортогональными осями: Entitlement (тир — что доступно по подписке), Role (роль — что можно делать), Scope (область видимости — над чем). Permission = Entitlement × Role × Scope. Устраняет необходимость в подролях (Администратор-1, Администратор-2) — это одна роль с разным scope. | active |
| DP.D.035 | Data Policy — политика данных IWE | Единая политика данных платформы: что собирается, где хранится, кому доступно, как удалить. Принятие — при установке шаблона (setup.sh). Агрегирует DP.D.028, DP.D.031, DP.ARCH.005, DP.ARCH.006, DP.ARCH.007 | active |
| DP.D.036 | BYOB Knowledge Architecture | Различение BYOB (Bring Your Own Backend) vs Managed: данные пользователя хранятся на его ресурсах, платформа даёт код и L2-знания. Связано с MCP Hub (ADR-018 v2) и контурами L2/L4. | draft |
| DP.D.037 | Рабочий продукт как инструмент связи | РП — не красиво оформленные данные, а инструмент, показывающий связь между элементами и работающий на достижение миссии | active |
| DP.D.038 | Обучение (Pre-Training) ≠ Онбординг (Context Engineering, НЕ HR-процесс) | — | active |
| DP.D.039 | Обещание (= сценарий использования) ≠ Описание метода ≠ Сервис | — | active |
| DP.D.040 | Мировоззрение → Pack: аналогия художника | Художник кодирует мировоззрение в произведение. Профессионал кодирует доменное знание в Pack. Оба трансформируют внутреннее в описание | active |
| DP.D.041 | Статистическое знание (AI) ≠ Верифицированное знание (Pack) | — | active |
| DP.D.042 | Генерация паттернов (AI) ≠ Генерация смысла (человек) | — | active |
| DP.D.043 | Рынок знания (commodity) ≠ Рынок компиляции (растёт) | — | active |
| DP.D.044 | Смена роли в диалоге ≠ Расширение промпта | — | active |
| DP.D.045 | ЦД-CEO (Executive) ≠ ЦД-Model (Passive Replica) | — | active |
| DP.D.046 | Экзоскелет (Exoskeleton) ≠ Автопилот (Autopilot) | — | active |
| DP.D.047 | Квалификация (стадия развития) ≠ Тир (уровень оснащения) | — | active |
| DP.D.048 | Скрипт ≠ Агент | — | active |
| DP.D.049 | Лог ≠ Инцидент ≠ State file | — | active |
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
| DP.D.083 | Persistent TaskTracker (filesystem) ≠ Ephemeral TodoWrite (session memory) | — | active |
| DP.D.084 | Workspace-координация peer'ов ≠ Conversational-сессия peer'ов | — | active |
| DP.D.086 | Дистрибутив-bundle ≠ Дистрибутив-coupling | — | active |
| DP.D.087 | OAuth pending state in-memory ≠ OAuth pending state externalized (БД) | — | active |
| DP.D.088 | `environment.d` (декларативный, persistent) ≠ `systemctl --user set-environment` (императивный, ephemeral) | — | active |
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
| DP.D.100 | IWE как платформа доказательств ценности ≠ система продуктивности | — | active |
| DP.D.101 | Shared Module Sharing: Symlink (α) ≠ Submodule (β) ≠ Vendor Copy (γ / γ-prime) | — | active |
| DP.D.102 | Четыре канала событий IWE по семантике | — | active |
| DP.D.103 | Специализация агента через контекст ≠ специализация через дообучение | Два уровня специализированного агента: уровень 1 — универсальное LLM-ядро + роль в контексте (Pack + промпт); уровень 2 — дообученное LLM-ядро, роль запечена в веса. Разные оси: где живёт доменное знание. | active |
| DP.D.104 | Прогресс к награде ≠ Показ баланса | — | — |
| DP.D.105 | Pack-internal frontmatter check ≠ DS-level prose check (scope линтера) | — | active |
| DP.D.106 | Trigger по состоянию ≠ Trigger по счётчику (для архитектурных переключений) | — | active |
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
| DP.D.132 | Стажёр ≠ Участник сообщества (промежуточное состояние входа ≠ полная готовность) | — | active |
| DP.D.133 | Три уровня изоляции данных в IWE | Данные в IWE изолируются на трёх независимых уровнях: БД-уровень (vault-паттерн), schema-уровень (aisystant schema), table/column-уровень (RLS + column grants). Каждый уровень защищает от разного класса нарушений. Уровни не заменяют друг друга — нарушение одного не компенсируется другим. | active |
| DP.D.134 | Logout (Kratos login-сессия) ≠ Отзыв OAuth-grant (Hydra) | — | active |
| DP.D.135 | Метод входа (authentication method) ≠ LLM-аккаунт (ресурсная подписка) | — | active |
| DP.D.136 | Предиктор выживания схемы ≠ качество дизайна | — | active |
| DP.D.137 | exocortex/CLAUDE.md slot (workspace-root backup) ≠ governance CLAUDE.md | — | active |
| DP.D.138 | Локализация-как-derived-only ≠ Два параллельных продукта | — | active |
| DP.D.139 | JIT provisioning через client-mediated flow ≠ direct-link flow | — | active |
| DP.D.140 | Наблюдатель-сторож (observability) ≠ Сервис доставки | — | active |
| DP.D.141 | Источник доставки ≠ Политика-на-место ≠ Физический транспорт | — | active |
| DP.D.142 | Тело контента (рендер-в-место) ≠ Уведомление о контенте (сообщение) | — | active |
| DP.D.143 | Интеграция как архитектурная конвенция ≠ Интеграция в открытом зонтике | — | active |
| DP.D.144 | Пустой output контрольной роли ≠ «нет находок» | — | active |
| DP.D.145 | Probe-канал (прямая проверка) ≠ Cascade detection (обнаружение через следствия) | — | active |
| DP.D.146 | Бизнес-алерт ≠ Технический алерт | — | active |
| DP.D.147 | Минимальный содержательный барьер (GREEN скрипта) ≠ Полное качество артефакта | — | active |
| DP.D.148 | Разбор «на бумаге» (walkthrough) ≠ Наблюдаемый артефакт (smoke) | — | active |
| DP.D.149 | Git (декларативный слой) ≠ Neon (операционный слой) ≠ Session (эфемерный слой) | — | active |
| DP.D.150 | Детерминированное пост-условие (в движок) ≠ Семантическое пост-условие (в Haiku) | — | active |
| DP.D.151 | Распределённая шина захвата следов ≠ Локальная шина (Local Gateway) | — | active |
| DP.D.152 | Corpus-split строчный тег (decision-уровень) ≠ Сессионный тег (session-уровень) | — | active |
| DP.D.153 | ICT-токен (ict_...) ≠ Ory OAuth (claude.ai / VS Code коннектор) | — | active |
| DP.D.154 | Топология орг-структуры IWE: iwesys ≠ aisystant ≠ mimecosys | — | draft |
| DP.D.155 | Active Day Definition | — | active |
| DP.D.156 | Операция агента, воспроизводимая ядром без него ≠ Авторитетная новая суть, закреплённая агентом | — | active |
| DP.D.157 | Прокси-на-пути (роутинг/расходы) ≠ Наблюдаемость-сбоку (трассировка/оценка качества) | — | active |
| DP.D.158 | Основное бизнес-событие ≠ Сторонний best-effort захват (деградация без блокировки) | — | active |
| DP.D.159 | Последовательная выкатка по направлению зависимости ≠ Параллельная выкатка (аддитивная фича через слои в разных репо) | — | active |
| DP.D.160 | Типизация (is-a, отнесение к роду) ≠ Синонимия (is-named) — семантика specializes в графе понятий | — | active |
| DP.D.161 | Выход фазы для машины-потребителя = структура в БД (VIEW) ≠ человекочитаемый отчёт | — | active |
| DP.D.164 | Метрика покрытия N/A (неприменима) ≠ Coverage PASS (критерий выполнен) | — | active |
| DP.D.165 | SPF (фреймворк вторых/доменных принципов) ≠ Pack (результат применения SPF) | — | active |
| DP.D.166 | Ось происхождения (Base/Pack/DS) ≠ Ось охвата (FPF/DPF/LPF) | — | active |
| DP.D.174 | Локальный pre-push хук ≠ серверный branch-protection | — | active |
| DP.D.175 | Зона суверенитета данных = машина владельца, не managed cloud | — | active |
| DP.D.176 | Учебный контент (guide/) ≠ Операционный контент (panel/) | — | active |
| DP.D.177 | Прокси-метрика ≠ прямая метрика | — | draft |
| DP.D.178 | LPF — сквозная классификация фрагментов ≠ физический контейнер (папка, репо) | — | active |
| DP.D.179 | Политика при сбое ≠ видимость сбоя в мониторинге | — | draft |
| DP.D.180 | Сгенерированный файл ≠ Живой файл — машиночитаемый критерий | — | active |
| DP.D.181 | Платформенный LLM-кошелёк ≠ личный BYOK-кошелёк | — | — |
| DP.D.182 | IWE как рабочая среда ≠ образовательная платформа | — | active |
| DP.D.183 | Машинный ноль в измеренном поле ≠ результат измерения «ноль» | — | active |
| DP.D.184 | Пустая витрина ≠ отсутствие данных в источнике | — | active |
| DP.D.185 | Разделение прав = сдерживание (containment), ≠ устранение причины | — | active |
| DP.D.186 | Документация снижает вероятность ошибки ≠ инвариант в коде устраняет её физически | — | active |
| DP.D.187 | SYNC-CORE (общее ядро инструкций) ≠ Claude-specific inject-hook | — | active |
| DP.D.188 | Возраст процесса ≠ Зависание процесса | — | active |
| DP.D.189 | no ≠ conditional-no ≠ defer (три статуса готовности системы к охвату) | — | active |
| DP.D.190 | `updated_at` строки-контейнера ≠ актуальность данных внутри строки | — | active |
| DP.D.191 | Mitigation ≠ Fix: статус дефекта остаётся открытым при снижении риска без устранения причины | — | active |
| DP.D.192 | Per-event rule engine ≠ stateful accumulation: разные вычислительные модели, разные компоненты | — | active |
| DP.D.193 | Имя поля/константы ≠ семантика текущей операции | — | active |
| DP.D.194 | Sanity check ≠ валидация на реальном масштабе | — | active |
| DP.D.195 | U.Method холоничен — U.Role нехолонична | — | — |
| DP.D.196 | Org Role Assignment Vs Infra Readiness | — | draft |
| DP.D.198 | Вычисляемый статус ≠ характеристика (строка таблицы критериев) | — | active |
| DP.D.199 | RLS-политика ≠ Защита для роли с BYPASSRLS | — | active |
| DP.D.200 | FORCE ROW LEVEL SECURITY ≠ Защита от роли с атрибутом BYPASSRLS | — | active |
| DP.D.201 | Защита-в-коде (флаг + тест) ≠ Требование-в-документе | — | active |
| DP.D.202 | Логическое «готово» ≠ Операционное «готово» | — | active |
| DP.D.203 | iCloud app container ≠ iCloud Drive — данные iOS-приложений доступны только через API | — | active |
| DP.D.204 | Method Map Vs State Axis | Каталог методов изменения состояния объекта — слой рычагов внутри существующей оси, не новое измерение модели состояний. Тест: это состояние объекта или инструмент воздействия на состояние? | draft |
| DP.D.205 | WP-ID vs PII — публичный идентификатор задачи ≠ персональные данные | Номер задачи (WP-\d+) — публичный реестровый идентификатор, не PII. Блокирование WP-номеров PII-guard = false positive. Тест: может ли пользователь сам упомянуть эту строку в публичном чате без раскрытия приватных данных? | active |
| DP.D.206 | git notes ≠ видимый аудиторский след | — | active |
| DP.D.207 | Config, документирующий себя «для будущей миграции», = заброшенность, не черновик | — | active |
| DP.D.208 | Проверка, читающая всегда-пустое поле = тавтологический pass (data-blocker) | — | active |
| DP.D.209 | Дрейф производного реестра = разность множеств (orphan-ID), не regex «найден ID» | — | active |
| DP.D.210 | Тонкий клиент сигналит через нейтральную шину ≠ тонкий клиент с write к доверенному хранилищу | — | active |
| DP.D.211 | Параметр генерации как машиночитаемое поле-контракт (тестируемо) ≠ инструкция только в промпте | — | active |
| DP.D.212 | Gateway (требует Bearer для L2) ≠ Прямой бэкенд (отдаёт L2 анонимно) | — | active |
| DP.D.213 | Категориальный policy-факт (сигнал вышестоящей роли) ≠ Семантика пути чужого домена (контекстное правило) | — | active |
| DP.D.214 | Durable user-opt-out (у Доставщика) ≠ Time-boxed policy-fact навигатора (в движке) | — | active |
| DP.D.215 | Асинхронный policy-fact на следующий батч ≠ Синхронный governor (для capped-класса доставки) | — | active |
| DP.D.216 | Outcome-DoD (результат работает у получателя) ≠ Output-DoD (артефакт поставлен) | — | active |
| DP.D.217 | dev-checkout resource ≠ prod-runtime resource → read-side snapshot | — | active |
| DP.D.218 | regex 'has-data-in-format' ≠ 'success-without-data' in shell output detection | — | active |
| DP.D.219 | Compression ≠ Lazy — различения без advance-signal требуют split hot/warm, а не lazy-load | — | active |
| DP.D.220 | Режим отказа lazy-компонента = деградация (capability недоступна в сессии) ≠ блокировка работы | — | active |
| DP.D.221 | Качественный порог мастерства ≠ Счётчик очков/частоты | — | active |
| DP.D.222 | Non-blocking аудит (рекомендательный канал) ≠ Слой защиты (blocking-by-default) | — | active |
| DP.D.223 | Видимое узкое место (наглядный дефицит) ≠ Связывающее ограничение (TOC bottleneck) | — | active |
| DP.D.224 | Сужение объёма (scope) как дешёвый рычаг ≠ Сужение объёма, когда объём = носитель сигнала | — | active |
| DP.D.225 | Просроченный шлюз — блокирующее предусловие (сбой) ≠ Восполнимая разведка (задержка) | — | active |
| DP.D.226 | Уровень принципов (степень общности: ZPF/FPF/SPF/TPF) ≠ Слой репозиториев (место хранения: Base/Pack/DS) | — | active |
| DP.D.227 | Singular-ресурс (0..1 на владельца) ≠ Plural-ресурс (0..N на владельца): безопасность confirm-скопом зависит от кардинальности | — | active |
| DP.D.228 | Способ исполнения как поле-данные авторитетного каталога ≠ магическая строка/конвенция имени файла в коде | — | active |
| DP.D.229 | Жёсткий отказ при отсутствии обещанного исполнителя ≠ тихий fallback в дорогую модель | — | active |
| DP.D.230 | Тест на гард/валидацию обязан падать без фикса ≠ тест декорирует гард (зелёный ≠ гард работает) | — | active |
| DP.D.231 | Owned узкий контракт enqueue (producer → API/RPC) ≠ raw cross-service SQL write | — | active |
| DP.D.232 | Единый писатель append-only журнала (один writer-модуль) ≠ N независимых писателей | — | active |
| DP.D.233 | Тихое исключение при деплое (silent-exclude) ≠ Уведомление об устаревании (notify-deprecate) | — | active |
| DP.D.234 | Метод сборки (логика компиляции) ≠ Место исполнения (резидентность данных) ≠ Поверхность отображения (витрина/репо) | — | active |
| DP.D.235 | Создание артефакта-руководства (единоразовая работа) ≠ Конвейер его обновления (recurring process) | — | active |
| DP.D.236 | Доставка обучения к рабочему контексту ≠ Производственное действие как шаг развития | — | active |
| DP.D.237 | Уникальный элемент связки ≠ Уникальная связка как целое (Bundle Moat) | — | active |
| DP.D.238 | Смещённый прокси ≠ честное «не знаем» | — | active |
| DP.D.239 | Pack-тип ≠ DB-таблица ≠ DB-строка | — | active |
| DP.D.240 | domain event ≠ interpretation of fact in immutable log | — | active |
| DP.D.241 | Реестр (registry) ≠ Продовое состояние (production state) | — | active |
| DP.D.242 | Событие перехода (per-transition) ≠ Событие состояния (per-state) | — | active |
| DP.D.243 | Контур IWE ≠ Физическое место ≠ Доверие/Контроль (Substrate Criterion) | — | active |
| DP.D.244 | Формализуемое знание (Pack/FPF/SPF) ≠ LPF-знание (встроено в личный опыт носителя роли) | — | active |
| DP.D.245 | Владение (универсальное ≠ персональное) ≠ Держатель (мы курируем ≠ пользователь принёс) — источники Портного | — | active |
| DP.D.246 | indexable knowledge ≠ pointer record | — | active |
| DP.D.247 | local user compute ≠ AI/human work in our zone | — | active |
| DP.D.248 | `last_verified` timestamp ≠ доказательство реальной верификации | — | active |
| DP.D.249 | SC (Service Clause) ≠ Метод верификации (Verification Method) | — | active |
| DP.D.250 | Владелец домена с архитектурными полномочиями ≠ Владелец домена без архитектурных полномочий | — | active |
| DP.D.251 | Service-Layer Permission ≠ Execution-Layer Obligation | — | — |
| DP.D.252 | Ступень (stage) ≠ Степень квалификации (qualification degree) | — | active |
| DP.D.253 | Модель тиров объектов IWE (платформенное / командное / личное) | — | active |
| DP.D.254 | PublicShelf (только published) ≠ Workshop (черновики + мастерская) — split контент-репо по lifecycle-статусу | — | active |
| DP.D.255 | Один механизм в двух местах ≠ два слоя defense-in-depth (барьеры независимы только при семантической разнице) | — | active |
| DP.D.256 | Поле-принуждение в редактируемом конфиге = договорённость, не принуждение (схема проверяет формат, не provenance) | — | active |
| DP.D.257 | Синхронизированный файл ≠ локальный файл | — | — |
| DP.D.258 | PaaS под управлением пользователя ≠ SaaS-агрегатор | — | — |
| DP.D.259 | scaffold-инициация ≠ point-capture ≠ онтологическая интеграция (три роли производства Pack-знания) | — | active |
| DP.D.260 | «Обнаружение → действие» контур ≠ «литерал → вычисленное» (диагностика gap-ов в скаффолдах) | — | active |
| DP.D.261 | Parliament Model (Платформа) ≠ Local Coordination Hub (IWE) | — | active |
| DP.D.262 | Platform (L2) ≠ IWE Template (L3) ≠ Personal IWE (L4) — контуры системы | — | active |
| DP.D.263 | Контуры (L1-L4) ≠ Тиры (T0-T4 + TM/TA/TD) | — | active |
| DP.D.264 | Method (уровень роли) ≠ Instrument (уровень назначения) | — | active |
| DP.D.265 | Кастомизация агента (Harness Engineering) ≠ Дообучение (Fine-Tuning) | — | active |
| DP.D.266 | Баллы (геймификация) ≠ Бонусы (лояльность) ≠ Ступень (квалификация) | — | active |
| DP.D.267 | Context Engineering ≠ Prompt Engineering | — | active |
| DP.D.268 | In-memory session state (auto-cleanup on redeploy) ≠ Persistent session state без TTL (zombie accumulation) | — | active |
| DP.D.269 | content_role ≠ process_position (двухосная модель роли в multi-agent peer-сессии) | — | active |
| DP.D.270 | Косметический баг ≠ Operational alert | — | active |
| DP.D.271 | pool.acquire(timeout) ≠ command_timeout в asyncpg | — | active |
| DP.D.272 | IWE-в-браузере ≠ перенос в Claude.ai ≠ урезанная копия VS Code | — | active |
| DP.D.273 | Стратегическая доменная роль ≠ infrastructure-agent | — | active |
| DP.D.274 | Negative definition ≠ Positive definition (для кодификации craft) | — | active |
| DP.D.275 | Голос (Voice) ≠ Тон (Tone) агента | — | active |
| DP.D.276 | «Двери» ≠ «Полосы» в сегментации аудиторий | — | active |
| DP.D.277 | P2W: «Principles-to-Work» (deprecated) ≠ «Problem-to-Work» (canonical) | — | active |
| DP.D.278 | Прерывистый отказ ≠ Непрерывная деградация | — | draft |
| DP.D.279 | Шкала серьёзности ≠ Идентификатор находки | — | draft |
| DP.D.280 | Две оценки одного конструкта в разное время несравнимы без совпадающей методологии | — | draft |
| DP.D.281 | Разграничение upstream/downstream в data-pipeline: Разметчик vs Генератор | — | draft |

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
| DP.M.092 | Infra Artifact As Create Flow Step | — | superseded |
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
| DP.M.208 | Diagnostics до behavioral nudge при stuck-сегменте | — | superseded |
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
| DP.M.302 | Trusted-reference хранилище: immutable контракт + audit-таблица рядом | — | draft |
| DP.M.303 | Production DDL через gated-шаг: отдельный .sql файл вне application code | — | draft |
| DP.M.304 | Локальный импорт тяжёлой зависимости для optional backend | — | draft |
| DP.M.305 | Frozen formula hash: версия формулы как артефакт под change-control | — | draft |
| DP.M.306 | Честная деградация тайла панели: статусные коды вместо дефолтных значений | — | draft |
| DP.M.307 | Bootstrap mode метрики по N выборки: hidden / interval / point | — | draft |
| DP.M.308 | Reader Contract Check Before Gate Removal | — | active |
| DP.M.309 | Halliday Language Rule Routing | — | active |
| DP.M.310 | Три измерения консистентности при автопереводе | Автоперевод требует трёх ортогональных измерений: (1) текст (LLM translation), (2) граф понятий (термин-глоссарий), (3) стиль (per-language style base). Пропуск любого — специфический вид drift. | — |
| DP.M.311 | File-fallback из шаблона: graceful degradation при отсутствии Pack | Интерфейс-слой (FMT-шаблон) доставляет базовое поведение (стили, правила, шаблоны) пользователю двухуровневой цепочкой: сначала ищем в Pack (полный домен), при отсутствии — берём встроенный фолбэк из шаблона. Необязательный Pack перестаёт быть жёсткой зависимостью. | — |
| DP.M.312 | OAuth prompt=login: принудительная re-authentication через стандартный параметр | Если клиент держит refresh-токен/grant и при reconnect не показывает форму входа, добавление параметра prompt=login (RFC 6749) к OAuth-authorize URL заставляет identity-провайдер игнорировать существующую сессию и потребовать свежую аутентификацию. | — |
| DP.M.313 | Enforcement ladder: уровни и критерии promotion правил | Каждое правило системы существует на одном из 5 уровней enforcement (E0 существует — E4 блокирует merge). Промоция между уровнями — отдельная инженерная задача. Главный паттерн — E3 (ручное ревью) → E2 (CI-скрипт) при условии однозначной автоматической проверки. | — |
| DP.M.314 | Structural criterion over symbol heuristic | При дизайне lint/audit-скриптов для markdown-артефактов проверять AST-структуру (заголовки уровней + непустые блоки), а не символьные паттерны (пунктуация →, :, *). Символьные эвристики дают false-positive на заголовках и false-negative на альтернативных нотациях. | — |
| DP.M.325 | Radar Analog Search Before Build | — | draft |
| DP.M.326 | Crystallization Threshold | — | draft |
| DP.M.327 | Multi Level Lookup Diagnostic Precision | — | draft |
| DP.M.328 | Yaml Preload Pure Bash Lookup | — | draft |
| DP.M.329 | Идемпотентность вебхука на уровне ограничения БД (ON CONFLICT DO NOTHING) | — | active |
| DP.M.331 | Agent Audit Trail as Append-only Sidecar | Машиночитаемый журнал действий агента хранится как отдельный append-only файл (audit-<id>.jsonl) рядом с человекочитаемым тредом сессии. Записывает события, которых нет в треде: вызовы инструментов, чтение/запись файлов, коммиты. Включается в коммит хода → переживает git reset --hard. | — |
| DP.M.332 | Sanity-guard quarantine pattern | Guard срабатывает двухслойно: (1) аномальная запись получает статус карантина, не финальный статус; (2) каждое срабатывание оставляет durable-след в инцидент-таблице. Уведомление эфемерно, инцидент-запись — нет. Решение о финализации отделено от детекции. | — |
| DP.M.333 | Failure mode matrix per event type | Явная таблица 'тип события → режим отказа' как артефакт дизайна до реализации. Юридически значимые события (согласие) при сбое инфраструктуры принимаются в очередь и дозаписываются; привилегии честно ждут. Один дефолт для всей системы = архитектурная ошибка. | — |
| DP.M.334 | Commented-out code with explanation as primary evidence of intentional disablement | При расследовании silent data gap первый поиск — git log --pickaxe + grep закомментированных строк с объяснением в планировщиках и main entry points. Закомментированный код с явным объяснением = primary evidence того, что компонент намеренно остановлен при миграции. | — |
| DP.M.335 | Adversarial Layered Review for Security-Critical Components | Для security-critical компонентов первое «полное» решение — baseline для adversarial review, не финал. Peer последовательно ищет attack surface в принятом fix; каждый новый fix открывает следующую поверхность. 2-3 раунда существенно меняют архитектуру решения. | — |
| DP.M.339 | Break-glass Key Distribution via Agent | При создании аварийного (break-glass) ключа агент играет роль генератора и передатчика: приватная часть показывается пилоту один раз, локальная копия удаляется. Хранение break-glass ключа — исключительно за человеком. Агент генерирует автоматический ключ и закрытый аварийный ключ в одной операции, но логика хранения у них разная. | — |
| DP.M.340 | Skill Resource Guard: Open-first, Close-last | Любой скилл, изменяющий файлы, открывает ресурс-гуард как первый шаг (до первого Write/Edit в сессии) и закрывает как последний шаг (после push, best-effort). Паттерн предотвращает конкурентный доступ нескольких агентов к одним и тем же файлам в мульти-агентной среде. | — |
| DP.M.341 | Verify Existing Security Pattern Before Implement | — | — |
| DP.M.342 | Grant Execute Not Direct Table Access Via Security Definer | — | — |
| DP.M.343 | App User Id Set Local Rls Bridge Shared Role | — | — |
| DP.M.344 | Pre-apply discovery: фактический delta прода до применения схемы | — | active |
| DP.M.345 | Render-Checklist Separation from Generator Script | — | active |
| DP.M.346 | Dual Lens Publication Verification | Два параллельных субагента разной природы (конформность фреймворку / адверсарный SOTA-поиск) перед публикацией — диверсификация линз ловит разные классы ошибок, которые одна проверка не обнаруживает. | — |
| DP.M.347 | Portrait First Reference Fallback | Рендер персонализированного артефакта: сначала читается пользовательский контекст (portrait), при отсутствии — graceful fallback на общий справочник. Интерфейс результата одинаков независимо от источника. | — |
| DP.M.348 | Content First Audit Phase Order | — | — |
| DP.M.349 | Commit Msg Guard Bypass Tags | — | — |
| DP.M.350 | Idempotent SQL migration: DDL-guards для safe re-run | Каждый DDL-оператор в миграции должен быть idempotent: CREATE TABLE IF NOT EXISTS, DROP IF EXISTS, DO $$ BEGIN ... EXCEPTION WHEN duplicate_object ... END $$ для CREATE ROLE / GRANT. Принцип: миграция безопасна для N-кратного выполнения с одинаковым конечным состоянием. | — |
| DP.M.351 | Neon pgbouncer advisory lock: 20×30s retry + infinite reconnect outer loop | — | active |
| DP.M.352 | Pull-based offline-consent handover | При split cloud/local storage с ограниченным consent-режимом: облако пишет stub+TTL, возвращает 'принято в ожидании'; локальный инстанс при reconnect вытягивает и подтверждает. Адаптер stateless. Альтернатива push = потеря данных при offline. | — |
| DP.M.353 | Event dedup window in routing catalog | Окно дедупликации события (dedup_window) зависит от семантики типа события, а не от архитектуры компонента. Параметр должен жить в routing_catalog.yaml рядом с маршрутами, не хардкодиться в Учётчике/воркере. Тест: можно ли изменить окно без деплоя компонента? | — |
| DP.M.354 | consent_unverified infra-fault write-path | При недоступности consent-DB на write-path: записывать с меткой consent_unverified, не блокировать запись. Infra-сбой ≠ policy-deny. Fail-closed при infra-сбое = SPOF. Downstream аудитор видит метку и принимает решение. | — |
| DP.M.355 | Reflex classify-before-llm-invoke dispatcher gate | В двухконтурном диспетчере (рефлекс + LLM) classify() по сигнатуре признаков должна вызываться ДО invoke_claude(). Нарушение: рефлекс-маршрут не экономит токены и не снижает latency. Дополнительный инвариант: confidence=0.0 для LLM-пути (неопределённая уверенность). | — |
| DP.M.356 | Routing Gate: extend existing convention over parallel structure | При появлении нового типа контента/артефакта — сначала проверить существующую конвенцию (grep по SoT). Если существующий тип можно расширить новым полем frontmatter — расширять, не создавать параллельную структуру. Тест: «добавить поле к существующему типу = тот же результат?» Да → параллельная структура не нужна. | — |
| DP.M.357 | Multistage pipeline carry-over: starvation guard via accumulators | Накопители между стадиями конвейера держат carry-over между итерациями: неизрасходованные единицы переходят в следующий прогон. Защищает от голодания последующих стадий при сбое предыдущей. Проектировочный инвариант: узкое место = самая медленная производящая стадия → автоматизировать первой. | — |
| DP.M.358 | Grep existing planning slots before creating new ones | Перед добавлением нового слота/секции в систему планирования (DayPlan, WeekPlan, Strategy) — grep по шаблонам. Если слот с похожим назначением уже есть — наполнять его, не создавать параллельный. Нарушение = дрейф (OwnerIntegrity): два слота с одним смыслом. | — |
| DP.M.359 | Static delta-aware lint for bare-commit guard | Grep по изменённым .claude/skills/** и scripts/** на bare `git commit -m` без pathspec; срабатывает только при коммите, вносящем новый bare-commit паттерн. Нет lifecycle, нет стейта, ловит регрессию. | — |
| DP.M.360 | Server Side Auth Context Enrichment | — | — |
| DP.M.361 | Histogram before reclassify | Перед переклассификацией команды/эндпоинта или изменением порога алерта собрать распределение латентности (P50/P90/P99) за N дней. Единичный алерт = выборка из 1, недостаточно для решения. | — |
| DP.M.362 | Prefetch snapshot empty guard | Когда prefetch-запрос возвращает all-empty результаты (сбой сети/сервиса), не перезаписывать рабочий снапшот. Рабочий снапшот сохраняется и используется downstream-конвейером как fallback. | — |
| DP.M.363 | FSM reset via write-ahead new key | Для сброса FSM-сессии записывать новый ключ с временным суффиксом (write-ahead), а не удалять существующий. Следующее сообщение получает новый session_id → FSM не находит записи → чистый контекст. Исключает race-condition при конкурентных сообщениях. | — |
| DP.M.364 | Offline-fallback с явным TTL снапшота | При загрузке кеш-снапшота проверить три условия последовательно: отсутствие файла / повреждение JSON / устаревание >TTL. При любом — вернуть None и переключиться на локальные seeds. Инвариант: pipeline никогда не блокируется из-за недоступности снапшота. | — |
| DP.M.365 | Граница безопасной компрессии инструктивного файла (peer-review как страж инварианта) | — | active |
| DP.M.366 | Detector Baseline Forward Only | Ретроспективное сканирование без ground truth даёт ложную уверенность. Baseline нового детектора ставится с даты первого события с явными метками, не с даты создания скрипта. | — |
| DP.M.367 | Per-Entity Advisory Lock for Concurrent Background Tasks | Два и более фоновых процесса, способных одновременно мутировать один и тот же объект, требуют advisory lock по идентификатору объекта. SELECT ... FOR UPDATE SKIP LOCKED обеспечивает serial execution для одного объекта без deadlock при параллельной обработке очереди. | — |
| DP.M.368 | Per-file byte/token ratio calibration | MEDIAN_RATIO = медиана (bytes(f)/tokens(f)) по всем файлам выборки — не одиночный вызов токенайзера на конкатенации. Граничные эффекты BPE при склейке дают смещённое effective ratio, а не константу пофайловой нормализации. | — |
| DP.M.369 | DRR Adequacy Pass | 6-вопросный чеклист ArchGate шаг 4.6: проверяет, что архитектурное/протокольное/продуктовое решение операционализировано, а не только записано. ≥1 ❌ → вернуться к детализации. | — |
| DP.M.370 | Dual-source behavioral scanner | Детектор поведенческого паттерна в логах с двумя независимыми каналами: tagged (явная метка, confidence=high) + pattern-matched (regex, confidence=low). Каналы репортируются отдельно, не суммируются. Baseline — с даты первого tagged-события, не с даты создания скрипта. | — |
| DP.M.371 | Байт/5 как прокси токенов при отсутствии API-доступа | — | draft |
| DP.M.372 | flock: один общий лок на весь проход vs per-item при последовательной внутренней обработке | — | — |
| DP.M.373 | Живое воспроизведение + снимок артефакта: детерминированное закрытие альтернативной гипотезы | — | — |
| DP.M.374 | Webhook Jwt Identity Provider Auth | — | — |
| DP.M.375 | Bidirectional Git Sync Split Timers | — | — |
| DP.M.376 | Source Scoped Candidate Pool Retrieval | — | — |
| DP.M.377 | Corpus Fanout Positioning Palette | — | forming |
| DP.M.378 | Triple Check Public Platform Text | — | forming |
| DP.M.379 | Условный запуск дорогостоящих шагов агента | — | draft |
| DP.M.380 | Retry-директива с форматным якорем | — | — |
| DP.M.381 | ВДВ-аудит конвейера на мёртвые Выходы | — | draft |
| DP.M.382 | Squash-коммит с атрибуцией автора при публикации в публичный репо | — | draft |
| DP.M.383 | Onboarding Work-Not-Learn Frame (Онбординг-фрейм «ты работаешь, не учишься») | — | active |
| DP.M.384 | Close Known Gap Immediately During Review (Немедленное закрытие known-gap при ревью) | — | active |
| DP.M.385 | Independent Agent Convergence as Confidence Signal (Совпадение независимых агентов = confidence signal) | — | active |
| DP.M.386 | Mentor Context-Sufficiency Gate | — | draft |
| DP.M.387 | Row Digest без mutable-полей для GDPR-совместимого ретро-якорения | — | active |
| DP.M.388 | Backward compat при rename ключа Digital Twin | — | active |
| DP.M.389 | Диагностика OAuth через последовательное исключение гипотез | — | active |
| DP.M.390 | Keyword-триаж как масштабируемый batch-метод Knowledge Extraction | — | active |
| DP.M.391 | M-мем антитезис как структурный крючок контента | — | active |
| DP.M.392 | Встраивать паттерн из каталога в текст без называния кода | — | active |
| DP.M.393 | T0-guard: условная активация сервисной клаузы | — | active |
| DP.M.394 | 2-ступенчатый гейт совпадения в routing-vocab | — | active |
| DP.M.395 | --build-host обязателен при деплое Docker-образа с Apple Silicon в Railway | — | active |
| DP.M.396 | Карта целевой аудитории как инструмент контент-решений (Audience-Map-Driven Communication) | — | draft |
| DP.M.397 | Терминологическая миграция «работа, не образование» (Terminology Migration: Work Not Learn) | — | draft |
| DP.M.398 | Разные пути подключения к БД в одном репо — диагностика риска конфликта | — | draft |
| DP.M.399 | Checkpoint и resume-on-crash для batch-прогонов с внешним API | — | draft |
| DP.M.400 | BIGSERIAL-монотонность как tip-детектор без графового обхода | — | draft |
| DP.M.401 | «Какой ход открылся» — явный вопрос при закрытии РП | — | draft |
| DP.M.402 | World-research фаза перед дизайном нового типа конвейера — обязательна как Ф1 | — | draft |
| DP.M.403 | Наблюдаемый сигнал подтверждения State-Transition Gate | — | draft |
| DP.M.404 | Явный предмет рассуждения в Ритуале согласования | — | draft |
| DP.M.405 | Post-write самопроверка в shell-скриптах устраняет тихие пропуски | — | draft |
| DP.M.407 | Тест L4-Personal vs L2-Platform: работает ли компонент без нашей инфраструктуры? | — | draft |
| DP.M.409 | При закрытии gating-условия: проверить реализацию в смежных РП | — | draft |

## Work Products

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.WP.001 | Отчёт экстракции | Структурированный отчёт экстракции знаний с классификациями, предложениями и валидацией | draft |
| DP.WP.002 | Ubiquitous Language | Единый язык домена: глоссарий терминов, прорастающий во все артефакты — код, UI, документацию, тикеты, планы | draft |
| DP.WP.003 | DayPlan | Ежедневный план работы: приоритеты, бюджеты, carry-over с предыдущего дня | draft |
| DP.WP.004 | WeekPlan | Еженедельный план: итоги прошлой недели, РП текущей, бюджеты, контент-план, сверка со стратегией | draft |
| DP.WP.005 | WeekReport | Итоги недели (WeekReport) — отдельный документ недельного отчёта. *Примечание: в марте 2026 указан deprecated как prerejection для ОПТ-5, но впоследствии (РП297) формат восстановлен — используется как расчётный тайл панели F5. | active |
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
| DP.FM.131 | Incomplete Lifecycle Tooling Registry Rot | — | active |
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
| DP.FM.152 | tracked-dir-added-to-gitignore: Добавление отслеживаемой git-папки в .gitignore без untracking | — | — |
| DP.FM.153 | Перемежающийся 401 со статическим ключом = прокси или env, не ключ | — | — |
| DP.FM.154 | Commit-without-push на сервере → отложенная дивергенция | — | — |
| DP.FM.155 | Cross Db Trigger Boundary | — | active |
| DP.FM.156 | Agent Tool Check Before Pilot Escalation | Агент просит пилота выполнить авторизацию, пройти UI-шаги или дать доступ, не проверив сначала доступные инструменты — нарушение принципа 'инструменты до эскалации'. | — |
| DP.FM.157 | Cloud Backup Wrong Claude Layer | В многоуровневой топологии IWE облачный бэкап имеет доступ только к одному уровню (governance), но пишет в слот другого уровня (workspace-root) — каждый день правильный локальный бэкап перезаписывается неправильным. | — |
| DP.FM.158 | Xargs Word Splitting Spaces False Fail | Использование pipe-xargs для проверки существования файлов с пробелами в имени: «DayPlan 2026-06-13.md» разбивается на «DayPlan» и «2026-06-13.md» — оба несуществующих → false FAIL на каждом Day Close. | — |
| DP.FM.159 | Creation-flow gap between linked identity providers | Если две identity-системы связаны односторонним ETL (читает из Б, не пишет в Б), регистрация в системе А не создаёт идентичность в Б. Пользователь с аккаунтом А упирается в форму входа Б без аккаунта — UX-симптом «не принимает пароль». | — |
| DP.FM.160 | Интерфейс без ядра порождает галлюцинацию данных | Когда интерфейсный агент (бот, chatbot, voice UI) не подключён к источнику данных через tool-вызов, LLM не возвращает ошибку — генерирует правдоподобный ответ из параметров модели. Симптом неотличим от правильного ответа без сверки с SoT. | — |
| DP.FM.161 | pack-event-name-drift: Pack документирует выдуманное имя события, в коде другое имя | — | — |
| DP.FM.162 | РП-контекст: vapor-claim о готовом компоненте (дрейф карточки от кода) | — | draft |
| DP.FM.163 | Локально зелено, в CI красно: node_modules маскирует stale package-lock | — | draft |
| DP.FM.164 | jose JWKS DNS-failure маскируется под request timed out | — | active |
| DP.FM.165 | Foreground Shell Orphan Ide Extension | — | draft |
| DP.FM.166 | Schema Consumer Contract Breach | — | draft |
| DP.FM.167 | Тихий False от upstream отключает except-fallback | — | draft |
| DP.FM.168 | Метрика=0 для активного пользователя: code-review фильтра до проверки raw-данных | — | draft |
| DP.FM.169 | Тихий fallback в content pipeline: acceptance PASS при деградации содержания | — | draft |
| DP.FM.170 | Literal Guard Pattern Mismatch On Day One | Guard проверяет наличие коммита через жёсткое совпадение текста, но реальные коммиты используют другой синтаксис. Guard сломан в момент деплоя. | — |
| DP.FM.171 | Ui Visibility Vs Code Access | — | — |
| DP.FM.186 | Append Only Phantom Early Writer | — | — |
| DP.FM.187 | Raw Template Execution Silent Artifacts | — | — |
| DP.FM.188 | Shared Db Owner Nonattribution | — | — |
| DP.FM.189 | Hash Without Prev Chain False Immutability | — | — |
| DP.FM.190 | Validator No Enforcement Point | — | — |
| DP.FM.191 | Source-of-truth в смертной папке РП: закрытие РП ломает внешние ссылки | SoT-файл создан внутри рабочей папки РП; при закрытии РП уходит в архив — внешние ссылки из платформенных файлов становятся битыми без сигнала. | active |
| DP.FM.192 | Subshell Redirect Silences Exit Code | — | — |
| DP.FM.193 | Git Dead Hook Core Hookspath | — | — |
| DP.FM.194 | Launchd Stale Pid Port Occupation | — | — |
| DP.FM.195 | Retroactive history cleanup to hit deadline (ретроактивная чистка истории ради дедлайна) | — | active |
| DP.FM.196 | Deferred Sql In Auto Executed Migration File | — | — |
| DP.FM.197 | Replay Tool Misidentified As Incoming Buffer | — | — |
| DP.FM.198 | Crypto Shredding Not Gdpr Erasure | — | — |
| DP.FM.199 | Role Revoke Schema Owner Bypass | — | — |
| DP.FM.200 | Audit Log Missing Source Service | — | — |
| DP.FM.201 | Bsd Gnu Sed Ampersand Escaping | — | — |
| DP.FM.202 | Multiple Registries One Entity Drift | — | — |
| DP.FM.203 | Deployed Consensus Not Final Verification | — | — |
| DP.FM.204 | Multi Row Insert Forks Trigger Chain | — | — |
| DP.FM.205 | Fsm Intermediate State Without Exit Path | — | — |
| DP.FM.206 | Ddl In Ensure Schema Locks Every Run | — | — |
| DP.FM.207 | Grep Keyword Not Anchored To Header False Green | — | — |
| DP.FM.208 | Bash32 Ifs Tab Nosplit | — | draft |
| DP.FM.209 | Sql Injection Fstring Parameter | — | draft |
| DP.FM.210 | Zsh Bsd Grep Multiline False Green | — | draft |
| DP.FM.211 | Gitignore Env Pattern Incomplete | — | draft |
| DP.FM.212 | git filter-branch --all уничтожает refs/stash (multi-parent) | — | — |
| DP.FM.213 | git filter-branch на текущей ветке синхронизирует worktree — файлы физически удаляются | — | — |
| DP.FM.214 | Zsh Word Split Bsd Grep Multiline False Green | — | draft |
| DP.FM.215 | Semaphore Agent Id Race Parallel Sessions | — | draft |
| DP.FM.216 | Multi Owner Aggregate Policy Granularity | — | draft |
| DP.FM.217 | Shell Pid Not Agent Session Pid | — | draft |
| DP.FM.218 | Wrong Diagnosis Hides Real Bug | — | draft |
| DP.FM.219 | Type Cast In Where Breaks Index | — | — |
| DP.FM.220 | Health Check Ddl Side Effect False Fail | — | — |
| DP.FM.221 | Timezone Msk Utc Date Comparison False Nudge | — | — |
| DP.FM.222 | Verbal Permission Not Process Env | — | — |
| DP.FM.223 | Verifier Hallucinated Verdict with Zero Tool Calls | — | active |
| DP.FM.224 | Smart Sync Stub Exists Not Local | — | — |
| DP.FM.225 | rsync --delete с пустой delta-staging директорией уничтожает все артефакты | — | — |
| DP.FM.226 | git worktree add из remote-ветки создаёт detached HEAD — push без явного refspec падает | — | — |
| DP.FM.227 | Bash set -e: [ cond ] && cmd внутри функции теряет exemption — функция возвращает exit 1 | — | — |
| DP.FM.228 | Railway liveness probe падает на 401 при /health за auth | — | — |
| DP.FM.229 | LLM aggregator single-key SPOF: model-fallback список не защищает от ключа с limit | — | draft |
| DP.FM.230 | return вместо raise в async-воркере маскирует сбой через exit(0) | — | draft |
| DP.FM.231 | Ambiguous parameter name carrying two semantic axes → silent wrong result | — | active |
| DP.FM.232 | Phantom field: моделировать поле, которого нет в реальном выходе источника | — | draft |
| DP.FM.233 | Форма записи append-only журнала изменена без подъёма schema_version | — | draft |
| DP.FM.234 | Provider migration tail — offline scripts stay on old provider | После миграции online-путей на новый LLM/API-провайдер offline-скрипты (ingestion, cron, переиндексация, инструменты разработчика) молча продолжают использовать старый провайдер, пока не будут запущены вручную. | — |
| DP.FM.235 | Eager framework context bloat: служебный каркас агента съедает контекст-бюджет до полезной работы | — | draft |
| DP.FM.236 | Преждевременная инвалидация кеша: сброс ДО успешного promote | — | draft |
| DP.FM.237 | Provenance-стейт стража вне репо: решение работает на одной машине, невидимо CI | — | draft |
| DP.FM.238 | Self-referential exemption: whitelist исключений в одном репо с аудитором | — | draft |
| DP.FM.239 | Архитектурное решение не зафиксировано в VCS: тихий сбой без видимого следа | — | draft |
| DP.FM.240 | Откат одного параметра без связанного: риск перетекает в новую ветку | — | draft |
| DP.FM.241 | Полисемия термина-источника: новое различение наследует двойственность и порождает баг-спутник | — | draft |
| DP.FM.242 | Ложная аналогия по поверхностному сходству API: вынужденный констрейнт ≠ выбранная настройка | — | draft |
| DP.FM.243 | Детерминированный расчёт делегирован LLM: правдоподобная заглушка вместо результата | — | draft |
| DP.FM.244 | Staged-delete другого агента поглощается коммитом при multi-agent git-работе | — | draft |
| DP.FM.245 | Inline-комментарий не обходит валидатор присутствия литерала | — | draft |
| DP.FM.246 | Stale Active Wp In Memory Table | Quick Close не включает шага обновления MEMORY.md — закрытый РП остаётся в таблице «Текущая работа» как активный до следующего явного протокола. | — |
| DP.FM.247 | Payment Api Ambiguous Terminal Status | — | — |
| DP.FM.248 | Metric Threshold Unit Rename | — | — |
| DP.FM.249 | Env-var с устаревшим дефолтом переименованного репо | — | draft |
| DP.FM.250 | FETCH_HEAD race: параллельные git pull на одном рабочем дереве задваивают файл | — | — |
| DP.FM.251 | OnBootSec gap collapse: начальный зазор между таймерами схлопывается при долгой uptime | — | — |
| DP.FM.252 | git pull.rebase=true глобально переопределяет --ff-only, направляя команду в rebase-логику | — | — |
| DP.FM.253 | Инструмент читает legacy-источник после переезда source of truth — прод не видит новых данных | — | draft |
| DP.FM.254 | LLM silent truncation: finish_reason=length без проверки записывает частичный вывод | — | draft |
| DP.FM.255 | Transitive shim dependency: пакет используется только как HTTP-клиент для стороннего провайдера, блокирует CI | — | draft |
| DP.FM.256 | Conditional LLM cleanup: постобработка зависит от входных данных → частичная утечка разметки | — | draft |
| DP.FM.257 | Railway project-scoped токен: неверный заголовок `Bearer` вместо `Project-Access-Token` → 401 | — | draft |
| DP.FM.258 | Parallel agent branch switch in shared checkout | — | — |
| DP.FM.259 | Measurement gaming — form checklist completion without substance | — | — |
| DP.FM.260 | Check infrastructure path resolution error masked as logic error | — | — |
| DP.FM.262 | Shared Error Db Env Leak | — | — |
| DP.FM.263 | Git Copy Instead Of Move Cross Repo | — | — |
| DP.FM.264 | Housekeeping Semaphore Missing Slug | — | — |
| DP.FM.265 | Catch-all exception in fallback function masks systematic errors as normal behaviour | — | active |
| DP.FM.266 | Exit Code Not Structural Correctness | — | — |
| DP.FM.267 | Publish Job In Mirror Not Source | — | — |
| DP.FM.268 | Checkout Persist Credentials Overrides App Token | — | — |
| DP.FM.269 | Time Window Scope Gate Parallel Agents | — | — |
| DP.FM.270 | Bootstrap Migration Drift Multi Version | — | — |
| DP.FM.271 | Автоматический ремонт перезаписывает файлы с owner:user | — | — |
| DP.FM.272 | mtime как критерий scope-gate недостаточен при headless-операциях | — | — |
| DP.FM.273 | GitHub List API обрезает результат при превышении page size без явной ошибки | — | — |
| DP.FM.274 | psql без ON_ERROR_STOP=1 в heredoc: SQL-ошибка не останавливает скрипт | — | active |
| DP.FM.275 | Serverless DB cold-start: первый запрос после паузы транзиентно падает | — | active |
| DP.FM.276 | Regex верификатора LLM-вывода не учитывает вариативность пунктуации → ложный негатив | — | active |
| DP.FM.277 | Dispatch по структурному тегу вместо содержимого секции: ветка никогда не срабатывает | — | active |
| DP.FM.278 | Cloudflare AI Workers getEmbedding: HTTP 500 при таймауте — не логическая ошибка | — | active |
| DP.FM.279 | Day Open hook не транзитивно вызывает дочерние скрипты | — | active |
| DP.FM.280 | Cross Db Staging Tables Must Exist On Both Ends | — | candidate |
| DP.FM.281 | Iterative Edit Regression in Untouched Parts (Регрессия конкретности при итеративной правке) | При итеративной правке текста проверяется только дельта изменений, а не весь текст целиком. В результате нетронутые фрагменты регрессируют: ранее конкретные формулировки становятся абстрактными, потому что правка одного блока восстанавливает абстракцию в соседнем. | active |
| DP.FM.282 | github.event.commits Null on workflow_dispatch (Отсутствие commits при ручном триггере) | При запуске GitHub Actions через workflow_dispatch поле github.event.commits отсутствует (null), а не пустой массив. toJSON(null) возвращает строку «null», и jq .[] завершается ошибкой вместо пустого результата. | active |
| DP.FM.283 | Dead Pipeline Step with No Output Consumer (Мёртвый шаг конвейера: выход без потребителя) | Шаг конвейера объявляет Выход, но потребителя этого Выхода внутри контура нет. Шаг становится мёртвым: он выполняется, но его результат никем не используется. Типичный сценарий — нарушение порядка refresh → read: мёртвый шаг стоит между обновлением данных и их чтением. | active |
| DP.FM.284 | Dead Env Var with Live Secret (Мёртвая env-переменная с живым значением секрета) | При ротации или переключении LLM-бэкенда старый API-ключ остаётся как env-переменная в деплой-платформе. Код уже переключился на новый бэкенд и ключ не читает, но ключ живёт в сервисе: ненужный доступ, ложное ощущение безопасности. | active |
| DP.FM.285 | hasExtraUsageEnabled Invisible Overage (Невидимое автосписание за превышение квоты Claude Max) | Флаг hasExtraUsageEnabled: true в ~/.claude.json разрешает Anthropic автоматически списывать за превышение квоты подписки Claude Max. Флаг не виден в Claude Code UI — нужно проверять Anthropic Console. При расследовании регулярных API-расходов без явного кода-потребителя проверять этот флаг в первую очередь. | active |
| DP.FM.286 | Silent Semantic Loss on Hot-File Compression (Тихая потеря смысла при сжатии hot-файла) | При LLM-сжатии hot-файлов (CLAUDE.md, distinctions.md) агент теряет семантику правил или различений — без явной ошибки. Детектор противоречий фиксирует только downstream-симптом (путаницу терминов в действиях агента), а не upstream-событие (момент сжатия). Разрыв между потерей и обнаружением = 1-2 хода. | active |
| DP.FM.287 | Параллельное авторство одного Pack-файла двумя агентами → тихий дрейф версий | — | active |
| DP.FM.288 | Shell-гейт: NUL-разделитель не сгенерирован → цикл выполняет 0 итераций → exit 0 | — | active |
| DP.FM.289 | SQLite date() возвращает NULL: offset без двоеточия (+HHMM) не распознаётся | — | active |
| DP.FM.290 | Apple Health resting_heart_rate: несколько строк на дату — выбирать по received_at DESC | — | active |
| DP.FM.291 | Промпт/скрипт не мигрирован в service-репо при переезде протокола — тихая развилка версий | — | active |
| DP.FM.292 | JWT-single-flag: достаточен для 1 продукта, ломается при N типах | — | active |
| DP.FM.296 | Некорректный путь в routing-vocab молча блокирует KE — defer вместо accept | — | active |
| DP.FM.297 | Денормализованный счётчик/enum в шапке артефакта молча дрейфует от таблицы-факта в теле | — | active |
| DP.FM.298 | Lifecycle-скрипт ищет по устаревшему glob-паттерну — молчаливый no-op при смене конвенции | — | active |
| DP.FM.299 | Критерий верификатора без описанного addressed-пути делает механический PASS недостижимым | — | active |
| DP.FM.300 | Исключение кода retry-механизма поглощается внешним try/except | — | active |
| DP.FM.334 | Network hang in iterator loop | — | active |
| DP.FM.335 | Install/update скрипт безусловно перезаписывает конфиг пользователя | — | active |
| DP.FM.336 | Resource-picker отбирает по recency без фильтра active-status | — | active |
| DP.FM.337 | ArchGate проверяет новую функцию, не регрессию соседних сервисов | — | active |
| DP.FM.338 | Month-Range Set Filter Drops Middle Months | set{from.month, to.month} фильтрация пропускает средние месяцы при 30-дневном окне, охватывающем 3+ календарных месяца. Исправление: walk с инкрементом месяца и year rollover. | draft |
| DP.FM.339 | Diff-aware guard слеп к indirect-обходу (запрещённый вызов рождается в рантайме через переменную/генерацию) | — | draft |
| DP.FM.340 | Cross-repo импорт на уровне модуля без checkout-guard | — | active |
| DP.FM.341 | Шлюз согласия с отброшенным возвращаемым значением | — | active |
| DP.FM.342 | Exact-token replace без ограничения строкой H1 даёт ложные срабатывания при выравнивании ID | — | active |
| DP.FM.346 | wp-sync-bundle.sh слепой к markdown-зачёркиванию: done-строка выглядит как pending | — | draft |
| DP.FM.347 | Partial fix одного экземпляра bug-класса: создаёт иллюзию безопасности при живых соседних экземплярах | — | draft |
| DP.FM.348 | re.sub: пользовательский текст с backslash в replacement → crash (invalid backreference) | — | draft |
| DP.FM.349 | Lookup по значению поля YAML: count > 1 → молчаливый выбор случайного файла | — | draft |
| DP.FM.350 | Нетранзакционный DELETE+INSERT в индексаторе чанков теряет данные при прерывании | — | draft |
| DP.FM.351 | ON CONFLICT DO NOTHING молча обрывает цепочку parent-chunk | — | draft |
| DP.FM.352 | Живая интерактивная сессия агента вступает в гонку с headless-вызовами за OAuth-токен | — | draft |
| DP.FM.353 | Ошибочный путь env-var маскируется локальной копией скрипта | — | draft |
| DP.FM.354 | Сравнение хэшей для 3-way-merge файлов структурно не работает без базового хэша | — | draft |
| DP.FM.355 | Moonshot прямой API-ключ умер 401 — рабочий fallback: OAuth-подписка kimi login | — | draft |

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
| DP.SOTA.030 | Eam Agent Manifest Standard | — | draft |
| DP.SOTA.031 | Async Factory Deterministic Pipeline | — | draft |
| DP.SOTA.032 | Semantic Chunking Rag | — | draft |
| DP.SOTA.033 | Ai Learning Platform Commoditization 2026 | — | draft |
| DP.SOTA.034 | Bigtech Context Commoditization | — | draft |
| DP.SOTA.035 | Always-on AI Mentor — Honest Degradation Under Thin Context | Диагноз+рекомендация в чате по запросу, поверх личного профиля: field-паттерн — многошаговый orchestration (не один вызов модели), context-sufficiency gate ДО генерации (не самоотчёт модели inline), и системный риск — персонализация усиливает уверенные ложные ответы при скудном контексте, а не снижает | active |

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
| DP.CONCEPT.005 | User Data Pipeline Types | 4 типа конвейеров личных данных: 2.1 биометрика/устройства, 2.2 поведенческие следы платформы, 2.3 агентские следы и знания, 2.4 персональный контекст для агентских решений. Каждый конвейер требует отдельной политики хранения, согласия и residency. | draft |

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
| DP.KR.001 | Маршрутизация знаний IWE | Полная карта маршрутизации: какой тип контента куда записывать — от ZP до memory/, от Pack до 0.9.Inbox. Единый source-of-truth для агента и пользователя | active |
| DP.KR.002 | Пользовательская карта маршрутизации знаний | Карта маршрутизации разнотипного материала пользователя (4 онтологических класса + Praxis) → дом + режим (index/pointer/external). Отличие от DP.KR.001: та — агентская, ~22 платформенных типа; эта — пользовательская: материал жизни и работы. | active |
| DP.KR.030 | Принцип триады учёт-доступ-аудит | Три функции институционального контроля — Учёт, Доступ, Аудит — должны быть структурно разделены. Совмещение любых двух из трёх в одной роли нарушает принцип независимости контроля. KR.030 = foundation серии (delivered WP-214). KR.031–033 = refinement-принципы каждой ветки; отложены, создаются при отдельном РП по необходимости. Серия KR.030–039 зарезервирована. | active |

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
| DP.METHOD.054 | Метод кодирования и классификации IWE | Метод заведения, ведения, версионирования и отмены кодов и схем классификации (реестров, нумераций, префиксов) в IWE. Объединяет две дорожки: дизайн схемы (корректность — фасеты, владелец namespace, разделение ID и классификации) и принуждение схемы (выживание — ось механизмов E0-E3). Предиктор выживания схемы — стоимость нарушения, не качество дизайна; но хорошо принуждённая плохая схема остаётся плохой, поэтому обе дорожки обязательны. | active |
| DP.METHOD.055 | Метод безопасного автоматического git push (push-invariant) | Тройное условие перед автоматическим push в CI/CD или cron: clean tree AND ahead>0 AND behind==0. При behind>0 (diverged) — молча пропустить, сигнализирует pull-alert компонент. | active |
| DP.METHOD.056 | Pre-deploy аудит потребителей перед сменой имён MCP-инструментов | Перед деплоем gateway с изменёнными именами инструментов — grep по всем клиентским репо на старые имена. Деплой только атомарно с обновлением потребителей. | active |
| DP.METHOD.057 | Идемпотентные SQL-миграции | Миграция БД должна быть безопасна при повторном запуске: проверка «уже существует» перед созданием, проверка «ещё существует» перед удалением. | active |
| DP.METHOD.058 | Повтор и форк сессии агента | Восстановить контекст агентской сессии до выбранной точки решения, чтобы исследовать альтернативный путь (форк) или воспроизвести рассуждение (повтор). | active |
| DP.METHOD.059 | Bash 32 Portability Python3 Heredoc | — | draft |
| DP.METHOD.060 | Skill Promotion L2 To L1 | — | draft |
| DP.METHOD.061 | Incremental Architecture Seed Order | — | draft |
| DP.METHOD.062 | Skill Description Scope Guard | — | draft |
| DP.METHOD.063 | Wp To Pack Migration Flow | WP→Pack migration flow: WP-document = thinking workspace (mutable), Pack = canonical source of truth (stable). After crystallization — content migrates to Pack, WP moves to archive. | draft |
| DP.METHOD.064 | Outcome Gate Pending Status | gate:outcome-pending — formal interim phase status between 'mechanism verified' and 'prod behaviour confirmed'. Prevents premature phase closure when tests pass but production observation period not yet complete. | draft |
| DP.METHOD.065 | Verifier Before Assembly | Verifier-before-assembly: explicit source availability check before content generation. Returns missing_source:<name> flags instead of silently falling back to defaults. | draft |
| DP.METHOD.066 | Probe First | Probe-first: read-only разведка live-системы ДО реализации фазы, чья карточка делает проверяемые фактические утверждения о системе. | draft |
| DP.METHOD.067 | Honest Provenance Backfill | При backfill provenance-колонки для данных неустановленного происхождения: 'unknown'+'flagged' (очередь аудита), не правдоподобный-но-непроверенный источник. | draft |
| DP.METHOD.068 | Denormalize Provenance Column | При хранении провенанс-ссылки в сущности: денормализовать reference-колонку прямо в сущность, а не создавать отдельную canonical-таблицу-справочник — это избегает второго источника истины. | draft |
| DP.METHOD.096 | Phase Absorption By Child Wp | При росте дочернего РП в самостоятельный зонтичный — зонтичный-родитель явно передаёт конкретные фазы дочернему и переопределяет свой scope. Смешение scope между РП запрещено (OwnerIntegrity). | draft |
| DP.METHOD.097 | Explicit Date Arg Reproducibility | Скрипт-генератор с date-dependent output принимает дату явно как --date YYYY-MM-DD. datetime.now() как единственный источник даты запрещён: делает вывод нетестируемым и ретроактивный рендеринг невозможным. | draft |
| DP.METHOD.098 | Dedicated Style Gate Pipeline | Выделенный пост-генерационный gate с детерминированным словарём проверяет стиль/compliance LLM-вывода. Style-инструкция в промпте = 'попросить'; gate = 'проверить'. Оба нужны. | draft |
| DP.METHOD.099 | Local Gateway Render Extension | Local Gateway = тонкий MCP-сервер, расширяемый операциями над локальными файлами пилота. Каждое расширение = отдельный Service Clause. Ограничение: онлайн только при работающей машине пилота. | draft |
| DP.METHOD.100 | Testing As Specification | Тест задаёт форму ожидаемого выхода — это и есть спецификация. Хороший тест читается как требование: при этом входе — этот выход. Тест без observable assertion (assert True, вечно-зелёный тест) — не тест, а шум. Документация устаревает; тест — вместе с кодом. | draft |
| DP.METHOD.101 | Append Only Audit Journal Integrity | Hash-chain trigger + SELECT FOR UPDATE на stream_tip + JCS + OTS anchors = дизайн append-only аудит-журнала с tamper-evidence и serialized writes без гонки хешей. Применимо к любому журналу с требованием non-repudiation. | draft |
| DP.METHOD.102 | Principle Hierarchy Zpf Tpf | Иерархия ZPF→FPF→SPF→TPF→LPF классифицирует принципы по специфичности применения. Поиск: сверху вниз (от универсального к инстансу/носителю). Загрузка в агента: снизу вверх (LPF→ZPF), чтобы специфические перевешивали общие при конфликте. | draft |
| DP.METHOD.103 | Language Parametric Onboarding Route | — | — |
| DP.METHOD.104 | Lpf Role Substitution Test | — | — |
| DP.METHOD.105 | Rule By Function Not Location | — | — |
| DP.METHOD.106 | Mutation Test Honesty Check | — | — |
| DP.METHOD.107 | Rule By Template Structure | — | — |
| DP.METHOD.108 | Error Counter Scale Diagnostic | — | — |
| DP.METHOD.109 | Measurement Layer Check Before Data Wait | — | — |
| DP.METHOD.110 | Alert Repeat Ack Gate | — | — |
| DP.METHOD.111 | Date Failure From First Launch Log Boundary | — | — |
| DP.METHOD.112 | Silent Component Triage Necessity First | — | — |
| DP.METHOD.113 | Acl Companion Artifact Schema Pipeline | — | — |
| DP.METHOD.114 | Diagnostics On Transient Failure | — | — |
| DP.METHOD.115 | Storage Writer Diagnosis Via Grants | — | — |
| DP.METHOD.116 | Позиционирование user_id в хэш-цепочке при праве на забвение | Позиция user_id внутри vs снаружи хэшируемого содержимого — обязательная развилка дизайна для append-only журнала с PII и требованием GDPR right-to-erasure. | active |
| DP.METHOD.117 | Мёртвый форк скрипта: верификация grep + прямое удаление | Безопасное устранение расходящейся копии инструмента: grep-верификация отсутствия вызовов + прямое удаление. Wrapper-редирект отклоняется: маскирует ошибку, создаёт ложный легитимный вход. | active |
| DP.METHOD.118 | Peer Dispute First Source Verification | — | — |
| DP.METHOD.119 | Watchdog check guard order (последовательные guard-условия) | — | active |
| DP.METHOD.120 | Multi-session reconcile gate (явный reconcile-ход при N>2 параллельных сессиях) | — | active |
| DP.METHOD.121 | Admin Delete Immutable Log Session Local Bypass | Административное удаление записи из immutable append-only журнала: 4-шаговая атомарная транзакция с SESSION_LOCAL bypass триггера только-для-последней-записи. Применимо к любому append-only хранилищу с GDPR-требованиями. | draft |
| DP.METHOD.122 | Month Close Rebuild Strategic Context | — | — |
| DP.METHOD.123 | Migration Number Collision As Coordination Signal | — | — |
| DP.METHOD.124 | Stateless Windowed Recompute | — | — |
| DP.METHOD.125 | Guard Normalized Ratio Not Raw Numerator | — | — |
| DP.METHOD.126 | Context Freshness Flag | — | — |
| DP.METHOD.127 | Wp Next Step Guide Block | — | — |
| DP.METHOD.128 | Detector Selftest Synthetic Regression | — | — |
| DP.METHOD.129 | Quarterly Cadence Month Close Mod3 | — | — |
| DP.METHOD.130 | Atomic Upsert On Conflict Race Prevention | — | — |
| DP.METHOD.131 | IO/calc/store ingest pipeline pattern | Трёхслойный паттерн для идемпотентного data-ingest из внешних источников: IO-слой (чтение сырых данных), calc-слой (нормализация/агрегация без хранения), store-слой (запись только при не-None; transient-контракт: None = 'нет данных за период' → пропустить, не писать нули). Дополнительно: chunking батчей против таймаутов внешнего API. | established |
| DP.METHOD.132 | GitHub App over fine-grained PAT for team pipelines | При командной разработке (>1 человека имеют доступ к репо) → GitHub App обязателен для постоянных конвейеров. Fine-grained PAT привязан к личному аккаунту — команда не видит, не может ротировать; срок ≤1 год = дополнительный административный долг. GitHub App — org-scoped: видим и ротируется централизованно. | established |
| DP.METHOD.133 | GitHub Actions workflow permissions declaration | GitHub Actions workflow с `permissions: contents: write` не требует отдельной настройки прав на уровне репо. GITHUB_TOKEN получает права из декларации в workflow. Применимо ко всем workflow с push/commit/release операциями. | established |
| DP.METHOD.134 | Authored File Deferred Conflict Delivery | — | — |
| DP.METHOD.135 | Render Checklist Separate Artifact | — | — |
| DP.METHOD.136 | Archive Integrity Listing Baseline | — | — |
| DP.METHOD.137 | Staged Migration Read Path Deferred Delete | — | — |
| DP.METHOD.138 | Knowledge Atom Normal Form Multi Consumer | — | — |
| DP.METHOD.139 | Methodology Pilot One Document Before Corpus | — | — |
| DP.METHOD.140 | E2E Pipeline Shakedown | — | — |
| DP.METHOD.141 | Sota Sheet Lite Before Pack Name | — | — |
| DP.METHOD.142 | Единый реестр для одного типа сущности (Single Registry for Entity Type) | — | — |
| DP.METHOD.143 | Явная tolerate нефатального exit code в CI-скрипте | — | — |
| DP.METHOD.144 | Feature flag вместо удаления кода при dual-write cutover | — | — |
| DP.METHOD.145 | Parse Once Shell Config | Читать конфиг-файл (YAML/JSON) один раз в параллельные bash-массивы; последующие lookup — чистый bash без subprocess. Устраняет N×fork overhead при многократном обращении к одному файлу. | draft |
| DP.METHOD.146 | Activate On Prefix Lazy Load | Activate-on-prefix: каркасный компонент держится как hot-стаб (1-2 строки: триггер + инструкция); полный контент грузится хуком при обнаружении детерминированного лексического advance-signal в сообщении. Экономит контекст-бюджет. | draft |
| DP.METHOD.147 | Cashback Provider Api Strict Sum Validation | При интеграции с API-провайдером, проверяющим сумму по каталогу: передавать полную цену провайдеру, списывать бонусы отдельно после подтверждения (cashback-модель). Не пытаться передавать дисконтированную сумму. | draft |
| DP.METHOD.148 | Mascot Rest State Silence | Rest state companion/маскота = отсутствие действия, не новое эмоциональное состояние. Любой visible-сигнал покоя (пыль, потускнение) создаёт guilt trigger → disengagement. | draft |
| DP.METHOD.149 | Derived Artifact Reinterprets Canon Untouched | Когда производный артефакт конфликтует с базовым каноном: переосмысление живёт только в производном, канон не редактируется. Конфликт фиксируется в content-cleanup backlog. | draft |
| DP.METHOD.150 | Tiered Hot File Split | Разбивка HOT-файла, превышающего лимит (150 строк), на три уровня: Tier-A (полный текст, ≤15 критических позиций), Tier-B (одна строка на позицию, ~20), Tier-C (только имена). Полные тексты Tier-B/C — в warm-файле (lazy). Паттерн применим к любому HOT-файлу: роли, SOTA, чеклисты. | draft |
| DP.METHOD.151 | Classify Before Count | Перед объявлением объёма миграции (N объектов) — классифицировать весь пул по целевому атрибуту и получить реальный N'. SELECT count(*) WHERE target_attr до start. Предотвращает ложный прогресс: '4/65' при реальном объёме N'=12 читается как 6% вместо 33%. | draft |
| DP.METHOD.152 | Time Window Content Type Rotation | Time-window эвристика принудительной ротации трудоёмких типов контента: если за последние N дней нет принятого контента типа T_heavy → предложить T_heavy вместо резервного. Предотвращает систематическое вытеснение трудоёмких типов более лёгкими. | draft |
| DP.METHOD.153 | Semantic Constant Migration Handshake | При поэтапной миграции к новому consumer — вводить семантическую константу в общий контракт (Service Clause) вместо routing по строковым литералам. Константа объявляет намерение на стороне enqueue (legacy) и на стороне matcher (новый consumer). Тест: 'если поменяется строка — нужно менять в двух местах?' Да → нужна константа в контракте. | draft |
| DP.METHOD.154 | Bypass Class Taxonomy | Bypass-class taxonomy: для каждого класса обхода security-гарда — отдельный regression-тест, подтверждающий, что нужный слой его ловит. Пустой класс = непокрытый вектор атаки. | draft |
| DP.METHOD.155 | Deadline Triage Date Over Gates | При конфликте дата vs шлюзы vs сигнал — приоритет резки: сначала дата (если не внешнее обязательство), затем дополнительные требования, последним — носитель сигнала и блокирующие предусловия. | draft |
| DP.METHOD.156 | External Fact Resolution | Когда внутренний спор двух ролей сводится к одному внешнему факту, недоступному ни одной из них — прекратить перетягивание и вынести единственный точечный вопрос носителю факта, оба варианта оформив как готовые исполняемые ветки. | draft |
| DP.METHOD.157 | Two Axis Distinction Form | Различение, чья суть — 'это две разные оси', записывать двумя отдельными именованными строками + пояснительной строкой 'где:', а не одной слитной. Слитная форма воспроизводит ровно тот синоним, против которого заведено различение. | draft |
| DP.METHOD.158 | Or Gate Multi Track Maturity | Для систем с двумя независимыми осями прогресса (учебная ступень, квалификационный уровень) использовать OR-гейт с независимым порогом на каждой оси. AND-гейт исключает опытных пользователей, вошедших через альтернативный путь. | draft |
| DP.METHOD.159 | Авто-детект состояния через минимальные сигналы с одним вопросом при неоднозначности | — | — |
| DP.METHOD.160 | Аддитивный маршрутизатор новой оси без регрессии | — | — |
| DP.METHOD.161 | Процесс закрытия пишет входные данные для следующего периода | — | — |
| DP.METHOD.162 | Auth Layering Public Base | При слиянии двух сервисов с разными уровнями доступа — базой выбирать публичный (fail-closed по умолчанию), авторизацию добавлять поверх. Обратный подход (взять приватный и вырезать авторизацию) создаёт fail-open при ошибке конфига. | draft |
| DP.METHOD.163 | Ci Allowlist Over Blocklist | CI-защита конфигурации через allowlist (явный список разрешённых) надёжнее blocklist (grep по подозрительным именам). Blocklist пропускает нейтрально-именованные нарушения; allowlist блокирует любое имя вне списка. | draft |
| DP.METHOD.164 | Fail Closed Unknown Service Mode | При отсутствующем или неизвестном значении параметра режима сервиса — отказать с явной ошибкой (HTTP 500 / exit 1), не делать fallback в публичный режим. Тихий fallback = fail-open при ошибке деплоя. | draft |
| DP.METHOD.165 | Public Showcase Final Result Only | При интеграции личного репо с публичной витриной — ограничивать публикуемую область директорией финальных артефактов (guide/). Сырые данные (inbox, memory, профиль) не должны попадать в публичную область. | draft |
| DP.METHOD.166 | Read From Canonical Engine Table | Если в системе есть компонент, который пишет канонические данные в БД (engine), — читать оттуда, не из локального файла. Локальный файл для тестирования = кандидат на расхождение с production-путём. | draft |
| DP.METHOD.167 | Схлопывание однотемных записей MEMORY.md в hub-файл (hub-collapse) | — | — |
| DP.METHOD.168 | ResidencyGate: статическая декларация потребности данных + динамическое состояние согласия | — | — |
| DP.METHOD.169 | Corpus dedup prefilter must enumerate all prior passes | — | — |
| DP.METHOD.170 | Трёхслойная модель сбора персональных данных (privacy-first) | — | — |
| DP.METHOD.172 | Верификация самодостаточности EN-дистрибуции: три оси | — | — |
| DP.METHOD.173 | Независимый верификатор с изоляцией контекста | — | active |
| DP.METHOD.174 | Аварийный рычаг fail-closed: явная фраза-подтверждения, не булев флаг | — | active |
| DP.METHOD.175 | Взаимозависимые изменения БД деплоятся с наблюдаемым промежутком | — | active |
| DP.METHOD.176 | N параллельных субагентов с изоляцией контекста как adversarial quality gate | — | active |
| DP.METHOD.177 | Последовательные раунды верификации с изоляцией контекста | — | active |
| DP.METHOD.178 | User-facing tool closure requires live acceptance test | — | active |
| DP.METHOD.179 | SoTA-check at Pack creation step 1.5 to prevent domain fragmentation | — | active |
| DP.METHOD.180 | Pre Promotion Peer Gate | — | candidate |
| DP.METHOD.181 | Трёхуровневый вердикт проверки согласованности знаний: clear / moved-to-cold / gone | — | active |
| DP.METHOD.182 | Commit-time pre-commit hook как точка shift-left enforcement инварианта знаний | — | active |
| DP.METHOD.183 | Cache miss rate как первичная метрика наблюдаемости LLM-системы с prompt caching | — | active |
| DP.METHOD.184 | Optional-executable hook: L1 вызывает L3-расширение через проверку [ -x ] | — | active |
| DP.METHOD.185 | Subsection вместо самостоятельной команды при расширении инструментария | — | active |
| DP.METHOD.186 | FSM Read-Model как альтернатива permission-системе при N состояниях на пользователя | — | active |
| DP.METHOD.188 | Три поля реестра: tier / target_tier / migration_trigger | — | active |
| DP.METHOD.189 | Operator-first rollout: новая фича/модель — сначала на операторском аккаунте | — | active |
| DP.METHOD.190 | Retry-once-then-alert: алерт после одного retry для немедленной детекции регрессии качества | — | active |
| DP.METHOD.191 | Cherry-pick recovery: восстановление коммита с чужой ветки при параллельном merge | — | active |
| DP.METHOD.194 | R30 Assembly/Hybrid: различение-кандидаты — только из собственных SoTA-источников Pack | — | active |
| DP.METHOD.195 | Apply-captures session как триггер регистрации нового Pack и применения defer-кандидатов | — | active |
| DP.METHOD.196 | ArchGate: прецедент инцидента в том же артефакте весомее общего правила | — | active |
| DP.METHOD.197 | ArchGate: функциональная регрессия оператора перевешивает архитектурную чистоту | — | active |
| DP.METHOD.198 | Dual-writer с дизъюнктными диапазонами ID — управляемый паттерн | — | active |
| DP.METHOD.199 | Smoke-тест миграции под реальной ролью, не суперпользователем | — | active |
| DP.METHOD.200 | Backport в upstream-ветку как критерий закрытия хотфикса | Хотфикс считается закрытым только после backport в upstream/staging-ветку + verify-скрипт перед каждой волной доставки. | active |
| DP.METHOD.201 | Gate-consistency + детерминированный rerunner для автономного протокола | Идемпотентный scheduled protocol: перед запуском проверить существование артефактов (gate-consistency), при повторном прогоне с теми же входными — тот же результат без дублирования. | active |
| DP.METHOD.202 | Manifest-only version check | Pre-release gate: сравнить version в манифесте с первой строкой CHANGELOG, чтобы поймать забытый generate-manifest.sh до публикации. | active |
| DP.METHOD.203 | Status Field As Production Gate | Поле status в трекере backlog'а как формальный production gate: только записи со status=accepted попадают в pipeline исполнения. Rejected-записи не удаляются — формируют anti-pattern corpus. Применимо к любому конвейеру с регулярными артефактами. | draft |
| DP.METHOD.204 | Unified Collector Over Precomputed Cache | Если live-данные и historical-данные требуют одинакового парсинга — единый сборщик, читающий сырые данные при каждом запросе, дешевле, чем предкомпьютинг + дублирующий live-путь. Тест: 'нужна ли та же логика для live-данных?' | draft |
| DP.METHOD.205 | Acceptance Sampling для LLM Batch Output (30 проб, порог 90%) | — | draft |
| DP.METHOD.206 | Nullable draft_path как разделитель Intent-Accepted vs Artifact-Exists | — | draft |

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
| DP.ROLE.075 | Доставщик (Delivery Policy Layer) | Слой политики исходящих: единая воронка всех сообщений пользователю. Применяет глобальный потолок по классу, приоритет, дедуп-по-всем и hard-gate предпочтений, затем передаёт транспорту (DP.ROLE.044) для физической доставки. | draft |
| DP.ROLE.076 | Ревьюер инженерного стиля кода (Code Craft Reviewer) | Контрольный агент: по git diff семантически проверяет соответствие крафт-правилам P1-P9 (DP.SC.172), которые механический детектор не ловит. Read-only на код, пишет только в свой канал (лог стиля + отчёт). Advisory-вердикт с severity; не блокирует и не правит. | active |
| DP.ROLE.077 | Учётчик следов (trace-accountant) | Единственный authorized writer в learning.domain_event. Принимает следы от сенсорных адаптеров, применяет consent-guard, нормализует, маршрутизирует по route_catalog, управляет trace_stubs и reconciler-отчётом. | draft |
| DP.ROLE.078 | Владелец тира (Tier Authority) | Единственный компонент, который вычисляет и пишет traits.tier. Операционная роль: меняет состояние персоны. Носитель — user-profile-service. | draft |
| DP.ROLE.079 | Bot Agent Session Orchestrator | Оркестратор live-агентной сессии IWE через Telegram: выбирает исполнителя (Claude/Kimi/Hermes) через factory, ведёт lifecycle сессии (start→run→pause→resume→close), принуждает audit + domain-scope, возвращает артефакты в Telegram. Не путать с Диспетчером очереди задач (DP.ROLE.045). | active |
| DP.ROLE.080 | Владелец выдачи согласия на анализ данных (Consent Grant Authority) | Единственный компонент, который пишет scope=data_analysis в learning.consent_grant и эмитит consent_granted в public.domain_event. Не отвечает за revoke, не отвечает за text_analysis/typing_tracking (владелец — бот). | draft |
| DP.ROLE.081 | Ретранслятор канона | Механически переносит содержимое канона в целевую организацию при каждом изменении — по активному обещанию (перевод или зеркалирование), не решает, что и когда публиковать. | draft |
| DP.ROLE.082 | Руководитель продвижения | Владелец домена «служба продвижения» (5 систем, артикулированных Алёной 19 апр 2026) — наполняет доменное содержание типов без права менять архитектурный каркас Pack. Первый прецедент роли «владелец домена без архитектурных полномочий» в этом Pack. | draft |
| DP.ROLE.083 | External Access Grantor (выдающий пропуска внешним ИИ-клиентам) | Выдаёт и отзывает пропуска (ict_ токены) внешним ИИ-клиентам (claude.ai Connector, VS Code) на основе тира пользователя — единственная точка выдачи, что для push-, что для pull-инициации. | draft |
| DP.ROLE.085 | Разметчик типов пользовательских данных (Data Structurer) | Раскладывает произвольную файловую базу пользователя по типам 2.1-2.4 (устойчивые факты / поток событий / доменные знания / неформализуемый остаток), обязательно карантинируя всё, что не должно попасть в генерацию. Детерминированная программа, LLM — только для явно неоднозначных файлов. | draft |
| DP.ROLE.086 | Диспетчер отложенного запуска РП (WP Scheduler) | Ставит РП в очередь на запуск в заданное время (launchd, будит Мак из сна), запускает выбранного агента headless с жёстким таймаутом, пишет отчёт; падение одного запуска не останавливает очередь. Детерминированная программа, LLM не использует. | draft |
| DP.ROLE.087 | Куратор личного бренда (Personal Brand Curator) | Собирает кандидатов на темы и факты личного бренда из платформенного и публичного следа человека, ведёт профиль его бренд-репо (setup, идемпотентный мерж, cross-reference) и кладёт всё на разбор живому владельцу; решений о публикации не принимает | active |
| DP.ROLE.088 | Router — Маршрутизатор материала пользователя | — | draft |
| DP.ROLE.089 | Владелец домена без архитектурных полномочий (Domain Owner, No Arch Authority) | Принимает операционные решения по конкретным системам домена без права вводить новые типы сущностей или менять архитектуру — эти решения требуют явного архитектурного ревью. | active |
| DP.ROLE.090 | Инженер сопровождения (Sustaining Engineer) | Операционная роль писателя peer-сессии для дефект-кластеров: берёт согласованный план фиксов по пачке issues, выполняет минимальные правки с регресс-тестами, закрывает issues с диагнозом. Специализация Разработчика-исполнителя: вход — дефекты, не постановка фичи. Культурный аналог — sustaining engineering (в отличие от new-development engineering). | active |
| DP.ROLE.091 | Сторож данных (data-gap watchdog) | Специализированная операционная роль: фоновый скрипт, который периодически проверяет содержимое (не живость процесса) зарегистрированных потоков персональных данных — health.db, WakaTime — на разрыв свежести сверх числового порога, и еженедельно подтверждает на canary-факте, что сам не деградировал в no-op. Скрипт ≠ агент (фиксированный flow, без LLM). | active |
| DP.ROLE.092 | Аудитор знаний (knowledge-audit watchdog) | Специализированная роль: launchd-таймер запускает Экстрактора (R2, LLM) ежемесячно в режиме knowledge-audit на ротационный scope Pack-репозиториев, доставляет сводку отчёта в Telegram. Таймер сам — скрипт (фиксированное расписание); исполнитель проверки внутри — LLM-агент (R2), не скрипт: структурная/контентная оценка требует суждения, не детерминированной проверки. | draft |

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
| DP.SC.039 | Multipath | — | active |
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
| DP.SC.053 | Локальная сборка персонального руководства (приватный IWE-контур) | Для пользователя со своим IWE и строгим приватным контуром персональное руководство собирается ЛОКАЛЬНО (в среде пользователя), а не на платформе: сырой личный контент (РП, рефлексии, captures, current_request) не покидает контур пользователя. Платформа отдаёт только производные (derived) данные и универсальное знание через MCP-шлюз под явным consent. Один метод DP.M.241, исполняемый в IWE-локусе; managed-обещание платформы — DP.SC.164. | draft |
| DP.SC.054 | Process Runner | — | draft |
| DP.SC.055 | Разметка пользовательской базы знаний по типам данных | Для человека с произвольной файловой базой (Obsidian/Notion-экспорт/plain files/своя БД) — зарегистрированного в IWE или нет — Разметчик раскладывает материал по типам 2.1-2.4 (устойчивые факты / поток событий / доменные знания / неформализуемый остаток) и обязательно карантинирует всё, что не должно попасть в генерацию (чужие PII, секреты, платёжные данные). Работает полностью локально, без наших серверов. | draft |
| DP.SC.056 | Портативная сборка персонального руководства без серверов | Для любого человека с размеченной базой (выход DP.SC.055) портативный исполнитель роли Портной (R27) собирает персональное руководство полностью локально: ноль обязательных серверов и честный provenance — каждый факт руководства атрибутирован источником в журнале решений (decision_log); при нехватке обязательных данных исполнитель отказывает с диагностикой, никогда не выдумывает. | draft |
| DP.SC.057 | Маршрутизация знаний пользователя | На вопрос «куда положить X» IWE отвечает: дом + режим (index/pointer/external) + обоснование по карте DP.KR.002 — или честное «вне карты», не выдумывает дом. | draft |
| DP.SC.058 | Route skill — маршрутизация материала пользователя | Скилл /route отвечает на вопрос пользователя «куда положить X» — материал любого типа (файл, ссылка, заметка, репо) получает дом + режим + тест класса по DP.KR.002, или честное «вне карты». | draft |
| DP.SC.059 | Сторож разрывов данных (data-gap watchdog) | Пилот получает Telegram-алерт при превышении числового порога разрыва свежести в зарегистрированном потоке персональных данных (health.db, WakaTime) — и гарантию, что сам детектор не умер молча | active |
| DP.SC.060 | Живой доступ guide-kit к курируемым материалам платформы | Портной (R27, носитель — guide-kit, WP-483) дополняет фундаментальный материал (режим А) свежими курируемыми материалами платформы вместо устаревающей статичной копии: живым запросом к публичному MCP-слою платформы (guides-mcp/knowledge-mcp) для подключённого пользователя, снимком через отдельный сервис скачивания для автономного пользователя без платформы вообще. Один SC, два локуса доставки — не два разных обещания. Честная деградация при недоступности источника, не крэш. | draft |
| DP.SC.061 | Каденция аудита базы знаний (knowledge-audit) | Пилот получает ежемесячный отчёт о структурной проверке Pack-репозиториев (пробелы, дубликаты, битые ссылки, устаревшие записи) без ручного напоминания себе запустить проверку | draft |
| DP.SC.062 | Публикация курируемого манифеста в исследовательский репозиторий iwesys | Ретранслятор канона (DP.ROLE.081) публикует ЯВНО ПЕРЕЧИСЛЕННЫЙ манифест уже-английских файлов из рабочего governance-репозитория (не весь repo/docs, без LLM-перевода на лету) в целевой публичный репозиторий iwesys — третий режим существующей роли, для потребителей без диспозиции 'вся документация репозитория публична' (WP-493 Лаборатория, будущий РП495). | active |
| DP.SC.063 | Agent connect & select (простое подключение и выбор агента) | Пилот получает: (1) повторяемый чеклист подключения нового файлового агента к IWE за конечное число шагов, без изобретения процесса заново; (2) простой способ решить, какому из уже подключённых агентов отдать конкретную задачу, без нового инструмента поверх существующей инфраструктуры. | draft |
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
| DP.SC.157 | Оптимизированный вход в марафон | T1-пользователь получает первое занятие марафона за 4 действия от /start, без ручного ввода команд | draft |
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
| DP.SC.168 | Онбординг платформы — зонтичное обещание | — | draft |
| DP.SC.169 | conductor-lite | — | deprecated |
| DP.SC.170 | onboarder | — | draft |
| DP.SC.171 | conveyor-development | — | draft |
| DP.SC.172 | База инженерного стиля кода | Агент-разработчик выдаёт код craft-уровня (без перечисленных запахов) при написании кода в репозиториях IWE | active |
| DP.SC.174 | Диспетчер контекста стилей | Диспетчер вычисляет полный композитный ключ из сырого контекста хода (детектор канала + роль читателя), запрашивает фрагмент у реестра и инъектирует его в промпт до первого токена | draft |
| DP.SC.175 | Выбор стиля пользователем | Пользователь настраивает стиль по осям (модель Grammarly), пресетам или из текста-примера; выбор пишется как user_override_hash в каскад платформа→канал→пользователь и применяется со следующего хода | draft |
| DP.SC.176 | Табло показателей пользователя | Авторитетная панель показателей пользователя за прошедший день (5 тайлов РП414) считается вне критического пути (после закрытия суток), публикуется как зафиксированный факт дня со статусом свежести; потребители (Day Open, личная страница, фоновые роли) только читают, не пересчитывают | draft |
| DP.SC.177 | Доставщик (слой политики доставки) | Единая точка, через которую физически уходят ВСЕ исходящие сообщения пользователю — с глобальным потолком по классу, приоритетом, дедупом и enforce предпочтений. Даёт обещанию-транспорту (DP.SC.134) зубы. | draft |
| DP.SC.178 | Голосовой канал IWE (Talk Mode — ввод) | Голосовое сообщение в боте распознаётся в текст на границе канала; расшифровка всегда сохраняется как заметка (мысль не теряется), параллельно — best-effort ответ ассистента. Аудио не хранится, передача в Whisper раскрыта явно, отдельное согласие, текстовый fallback при сбое. | draft |
| DP.SC.179 | Семантический ревью соответствия инженерному стилю кода | Контрольный агент по git diff семантически проверяет соответствие правилам инженерного стиля P1-P9 (DP.SC.172), которые механический детектор не ловит (копипаста P2, мёртвый код P3, смешение обязанностей P5, наблюдаемость P6, неидиоматичность P8, ручной парсинг P9). Выдаёт advisory-вердикт с file:line + severity в единый лог стиля → метрика code-compliance. Никогда не мутирует код; gating — политика потребителя, не роли. | active |
| DP.SC.180 | unit-economics | — | draft |
| DP.SC.181 | Гард ID-коллизий в Pack-репо (pre-commit) | Delta-aware pre-commit гард блокирует коммит, если новый entity-файл занимает уже существующий код (PREFIX.TYPE.N), и подсказывает следующий свободный номер. Закрывает гонку параллельных агентов, независимо берущих max+1 в одной рабочей папке. Глобальный pack-lint остаётся warning, CI check-pack-collisions — бэкстоп. | active |
| DP.SC.182 | Учётчик следов (trace-accountant) | Принимает сырые следы от сенсорных адаптеров, проверяет consent, нормализует, маршрутизирует в домы знания по route_catalog, управляет stub-буфером offline+restrictive, ведёт reconciler-отчёт. Единственный authorized writer в learning.domain_event. | draft |
| DP.SC.183 | Bot Llm Dialog | — | draft |
| DP.SC.184 | Bot Day Open | — | draft |
| DP.SC.185 | Владелец тира (Tier Authority) | Единственный authoritative-источник уровня доступа (traits.tier T0-T4). Вычисляет, хранит и поддерживает актуальность тира персоны по lifecycle-событиям: подписка, AI-клиент, GitHub, admin. | draft |
| DP.SC.186 | Bot Agent Session | — | active |
| DP.SC.187 | local-gateway-render | — | draft |
| DP.SC.188 | Синхронизация IWE-шаблона с англоязычной проекцией | Каждое изменение README/docs личного русскоязычного шаблона IWE автоматически появляется переведённым в публичном английском репозитории iwesys/iwe-template — без участия автора и без отдельной команды на публикацию. | draft |
| DP.SC.189 | Зеркалирование методического контента aisystant в МИМ | Каждое изменение в одном из 11 методических репозиториев aisystant (docs, guides, main-docs и т.д.) автоматически публикуется как есть (без перевода) в парном репозитории организации МИМ, без штатного git-механизма fork/transfer и без публичного отображения родства между организациями. | draft |
| DP.SC.190 | Подключение внешнего ИИ-клиента к персональным знаниям | Человек с оплаченной подпиской (T3/T4) подключает claude.ai Connector или расширение VS Code к своим знаниям платформы одним действием — без ручной возни с токенами и конфигами. | active |
| DP.SC.191 | Capacity commitment must decompose by load type | — | — |
| DP.SC.192 | Scheduled WP launch queue must run unattended with hard resource guards | — | — |
| DP.SC.193 | Generated domain content must pass an ontological-vs-lexical specificity lens before acceptance | — | — |
| DP.SC.194 | Конвейер личного бренда (Personal Brand Pipeline) | Пользователь ступени Мастер+ получает управляемый контур личного бренда: репо бренда из шаблона, setup-сессию самоописания, регулярный сбор кандидатов на темы из его платформенного и публичного следа — с разбором живым владельцем и без выдуманных фактов | active |
| DP.SC.195 | Pack replenishment by topic must fix decisions as typed DR-records with maps_to before section text generation | — | — |
| DP.SC.196 | Content-cleanup signal registry must never lose a registered signal | — | — |
| DP.SC.197 | Наставник (Режим 2) — оперативная помощь в чате | Пилот в любой момент описывает своё текущее состояние своими словами в чате → Наставник (MIM.R.001, Режим 2 — связка Диагност⊗Навигатор⊗Преподаватель-предметник⊗Преподаватель-лидер) ставит диагноз и даёт рекомендацию, опираясь на известное о пилоте, или честно говорит, что знаний не хватает / даёт универсальный ответ | draft |

### SYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SYS.001 | Детерминированные системы | Реестр детерминированных подсистем. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |

### VM

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.VM.001 | P1 P9 Calibration Matrix | Девять промежуточных польз новичка: как система засекает достижение каждой (прокси/БД) и как Онбордер ведёт к ней (доставка/предусловие/характеристика Стажёра/событие тира). | — |

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
- Missing `summary`: DP.D.136 (DP.D.136-survival-predictor-vs-design-quality.md)
- Missing `summary`: DP.D.137 (DP.D.137-exocortex-claude-slot-workspace-vs-governance.md)
- Missing `summary`: DP.D.182 (DP.D.182-iwe-work-environment-vs-educational-platform.md)
- Missing `summary`: DP.D.183 (DP.D.183-machine-precision-zero-vs-measured-zero.md)
- Missing `summary`: DP.D.184 (DP.D.184-empty-view-vs-no-source-data.md)
- Missing `summary`: DP.D.186 (DP.D.186-doc-reduces-error-probability-invariant-eliminates-it.md)
- Missing `summary`: DP.D.187 (DP.D.187-sync-core-vs-inject-hook.md)
- Missing `summary`: DP.D.190 (DP.D.190-container-updated-at-vs-data-freshness.md)
- Missing `summary`: DP.D.191 (DP.D.191-mitigation-vs-fix-defect-status.md)
- Missing `summary`: DP.D.192 (DP.D.192-per-event-rule-engine-vs-stateful-accumulation.md)
- Missing `summary`: DP.D.193 (DP.D.193-field-name-vs-operation-semantics.md)
- Missing `summary`: DP.D.194 (DP.D.194-sanity-check-vs-scale-validation.md)
- Missing `summary`: DP.D.195 (DP.D.195-method-holonic-role-nonholonic.md)
- Missing `summary`: DP.D.196 (DP.D.196-org-role-assignment-vs-infra-readiness.md)
- Missing `summary`: DP.D.206 (DP.D.206-git-notes-vs-audit-trail.md)
- Missing `summary`: DP.D.207 (DP.D.207-self-documenting-migration-config-abandonment.md)
- Missing `summary`: DP.D.208 (DP.D.208-tautological-pass-always-empty-field.md)
- Missing `summary`: DP.D.209 (DP.D.209-derived-registry-drift-set-difference.md)
- Missing `summary`: DP.D.210 (DP.D.210-thin-client-signal-vs-write-trusted-storage.md)
- Missing `summary`: DP.D.211 (DP.D.211-generation-param-machine-readable-contract.md)
- Missing `summary`: DP.D.212 (DP.D.212-gateway-bearer-vs-direct-backend-anonymous-l2.md)
- Missing `summary`: DP.D.213 (DP.D.213-categorical-policy-fact-vs-domain-semantic-path.md)
- Missing `summary`: DP.D.214 (DP.D.214-durable-opt-out-vs-time-boxed-policy-fact.md)
- Missing `summary`: DP.D.215 (DP.D.215-async-policy-fact-vs-sync-governor.md)
- Missing `summary`: DP.D.216 (DP.D.216-outcome-dod-vs-output-dod.md)
- Missing `summary`: DP.D.217 (DP.D.217-dev-vs-prod-runtime-resource.md)
- Missing `summary`: DP.D.218 (DP.D.218-regex-data-presence-vs-success-signal.md)
- Missing `summary`: DP.D.240 (DP.D.240-domain-event-vs-interpretation-immutable-log.md)
- Missing `summary`: DP.D.246 (DP.D.246-indexable-knowledge-vs-pointer-record.md)
- Missing `summary`: DP.D.247 (DP.D.247-local-user-compute-vs-our-zone-ai-work.md)
- Missing `summary`: DP.D.248 (DP.D.248-last-verified-vs-actual-verification.md)
- Missing `summary`: DP.D.252 (DP.D.252-stage-vs-qualification-degree.md)
- Missing `summary`: DP.D.254 (DP.D.254-public-shelf-vs-workshop-lifecycle-split.md)
- Missing `summary`: DP.D.255 (DP.D.255-one-mechanism-two-places-vs-defense-in-depth.md)
- Missing `summary`: DP.D.256 (DP.D.256-enforcement-field-vs-convention.md)
- Missing `summary`: DP.ARCH.009-decisions (DP.ARCH.009-decisions.md)
- Missing `summary`: DP.D.001 (DP.D.001-obekt-vs-model.md)
- Missing `summary`: DP.D.002 (DP.D.002-model-vs-dannye.md)
- Missing `summary`: DP.D.003 (DP.D.003-dannye-vs-predstavlenie-view.md)
- Missing `summary`: DP.D.004 (DP.D.004-indikator-vs-fakt.md)
- Missing `summary`: DP.D.005 (DP.D.005-vhodnoi-indikator-vs-proizvodnyi.md)
- Missing `summary`: DP.D.006 (DP.D.006-generativnyi-tekst-vs-source.md)
- Missing `summary`: DP.D.007 (DP.D.007-kachestvo-modeli-vs-kachestvo.md)
- Missing `summary`: DP.D.008 (DP.D.008-sinhronizaciya-vs-kopirovanie.md)
- Missing `summary`: DP.D.009 (DP.D.009-ii-agent-vs-ii.md)
- Missing `summary`: DP.D.010 (DP.D.010-arhitekturnaya-harakteristika-vs-arhitekturnyi.md)
- Missing `summary`: DP.D.011 (DP.D.011-platform-space-vs-user.md)
- Missing `summary`: DP.D.012 (DP.D.012-znanie-vs-informaciya.md)
- Missing `summary`: DP.D.013 (DP.D.013-navigaciya-vs-poisk.md)
- Missing `summary`: DP.D.014 (DP.D.014-operativnaya-pamyat-vs-spravochnik.md)
- Missing `summary`: DP.D.015 (DP.D.015-ii-sistema-vs-it.md)
- Missing `summary`: DP.D.016 (DP.D.016-capture-to-pack-vs.md)
- Missing `summary`: DP.D.017 (DP.D.017-strategicheskii-ddd-vs-takticheskii.md)
- Missing `summary`: DP.D.018 (DP.D.018-model-vs-view-3.md)
- Missing `summary`: DP.D.019 (DP.D.019-dsl-vs-dslm.md)
- Missing `summary`: DP.D.020 (DP.D.020-capture-during-work-vs.md)
- Missing `summary`: DP.D.021 (DP.D.021-vai-b-modelirovanie-vs.md)
- Missing `summary`: DP.D.022 (DP.D.022-tri-urovnya-proveryaemosti-formalnaya.md)
- Missing `summary`: DP.D.023 (DP.D.023-rol-vs-agent-agent.md)
- Missing `summary`: DP.D.024 (DP.D.024-semantic-search-vs-keyword.md)
- Missing `summary`: DP.D.026 (DP.D.026-hranimyi-sche-tchik-vs.md)
- Missing `summary`: DP.D.038 (DP.D.038-obuchenie-pre-training-vs.md)
- Missing `summary`: DP.D.039 (DP.D.039-obeschanie-scenarii-ispolzovaniya-vs.md)
- Missing `summary`: DP.D.041 (DP.D.041-statisticheskoe-znanie-ai-vs.md)
- Missing `summary`: DP.D.042 (DP.D.042-generaciya-patternov-ai-vs.md)
- Missing `summary`: DP.D.043 (DP.D.043-rynok-znaniya-commodity-vs.md)
- Missing `summary`: DP.D.044 (DP.D.044-smena-roli-dialoge-vs.md)
- Missing `summary`: DP.D.045 (DP.D.045-cd-ceo-executive-vs.md)
- Missing `summary`: DP.D.046 (DP.D.046-ekzoskelet-exoskeleton-vs-avtopilot.md)
- Missing `summary`: DP.D.047 (DP.D.047-kvalifikaciya-stadiya-razvitiya-vs.md)
- Missing `summary`: DP.D.048 (DP.D.048-skript-vs-agent.md)
- Missing `summary`: DP.D.049 (DP.D.049-log-vs-incident-vs.md)
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
- Missing `summary`: DP.D.083 (DP.D.083-persistent-tasktracker-filesystem-vs.md)
- Missing `summary`: DP.D.084 (DP.D.084-workspace-koordinaciya-peer-ov.md)
- Missing `summary`: DP.D.086 (DP.D.086-distributiv-bundle-vs-distributiv.md)
- Missing `summary`: DP.D.087 (DP.D.087-oauth-pending-state-in.md)
- Missing `summary`: DP.D.088 (DP.D.088-environment-d-deklarativnyi-persistent.md)
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
- Missing `summary`: DP.D.100 (DP.D.100-iwe-kak-platforma-dokazatelstv.md)
- Missing `summary`: DP.D.101 (DP.D.101-shared-module-sharing-symlink-submodule-vendor.md)
- Missing `summary`: DP.D.105 (DP.D.105-pack-internal-frontmatter-check.md)
- Missing `summary`: DP.D.106 (DP.D.106-trigger-sostoyaniyu-vs-trigger.md)
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
- Missing `summary`: DP.D.132 (DP.D.132-stazher-vs-member.md)
- Missing `summary`: DP.D.134 (DP.D.134-logout-kratos-login-sessiya.md)
- Missing `summary`: DP.D.135 (DP.D.135-metod-vhoda-authentication-method.md)
- Missing `summary`: DP.D.138 (DP.D.138-lokalizaciya-kak-derived-only.md)
- Missing `summary`: DP.D.139 (DP.D.139-jit-provisioning-cherez-client.md)
- Missing `summary`: DP.D.140 (DP.D.140-nablyudatel-storozh-observability-vs.md)
- Missing `summary`: DP.D.141 (DP.D.141-istochnik-dostavki-vs-politika.md)
- Missing `summary`: DP.D.142 (DP.D.142-telo-kontenta-render-mesto.md)
- Missing `summary`: DP.D.143 (DP.D.143-integraciya-kak-arhitekturnaya-konvenciya.md)
- Missing `summary`: DP.D.144 (DP.D.144-pustoi-output-kontrolnoi-roli.md)
- Missing `summary`: DP.D.145 (DP.D.145-probe-kanal-pryamaya-proverka.md)
- Missing `summary`: DP.D.146 (DP.D.146-biznes-alert-vs-tehnicheskii.md)
- Missing `summary`: DP.D.147 (DP.D.147-minimalnyi-soderzhatelnyi-barer-green.md)
- Missing `summary`: DP.D.148 (DP.D.148-razbor-bumage-walkthrough-vs.md)
- Missing `summary`: DP.D.149 (DP.D.149-git-deklarativnyi-sloi-vs.md)
- Missing `summary`: DP.D.150 (DP.D.150-determinirovannoe-post-uslovie-dvizhok.md)
- Missing `summary`: DP.D.151 (DP.D.151-raspredele-nnaya-shina-zahvata.md)
- Missing `summary`: DP.D.152 (DP.D.152-corpus-split-strochnyi-teg.md)
- Missing `summary`: DP.D.153 (DP.D.153-ict-token-ict-vs.md)
- Missing `summary`: DP.D.154 (DP.D.154-iwesys-product-org-topology.md)
- Missing `summary`: DP.D.155 (DP.D.155-active-day-definition.md)
- Missing `summary`: DP.D.156 (DP.D.156-operaciya-agenta-vosproizvodimaya-yadrom.md)
- Missing `summary`: DP.D.157 (DP.D.157-proksi-puti-routing-rashody.md)
- Missing `summary`: DP.D.158 (DP.D.158-osnovnoe-biznes-sobytie-vs.md)
- Missing `summary`: DP.D.159 (DP.D.159-posledovatelnaya-vykatka-napravleniyu-zavisimost.md)
- Missing `summary`: DP.D.160 (DP.D.160-tipizaciya-is-otnesenie-rodu.md)
- Missing `summary`: DP.D.161 (DP.D.161-vyhod-fazy-dlya-mashiny.md)
- Missing `summary`: DP.D.164 (DP.D.164-metrika-pokrytiya-n-neprimenima.md)
- Missing `summary`: DP.D.165 (DP.D.165-spf-frei-mvork-vtoryh.md)
- Missing `summary`: DP.D.166 (DP.D.166-os-proishozhdeniya-base-pack.md)
- Missing `summary`: DP.D.174 (DP.D.174-lokalnyi-pre-push-huk.md)
- Missing `summary`: DP.D.175 (DP.D.175-zona-suvereniteta-dannyh-mashina.md)
- Missing `summary`: DP.D.176 (DP.D.176-uchebnyi-kontent-guide-vs.md)
- Missing `summary`: DP.D.177 (DP.D.177-proxy-metric-vs-direct.md)
- Missing `summary`: DP.D.178 (DP.D.178-lpf-skvoznaya-klassifikaciya-fragmentov.md)
- Missing `summary`: DP.D.179 (DP.D.179-failure-policy-vs-monitoring-visibility.md)
- Missing `summary`: DP.D.180 (DP.D.180-generated-vs-live-file-criterion.md)
- Missing `summary`: DP.D.181 (DP.D.181-platform-vs-byok-wallet.md)
- Missing `summary`: DP.D.185 (DP.D.185-razdelenie-prav-sderzhivanie-containment.md)
- Missing `summary`: DP.D.188 (DP.D.188-vozrast-processa-vs-zavisanie.md)
- Missing `summary`: DP.D.189 (DP.D.189-vs-conditional-vs-defer.md)
- Missing `summary`: DP.D.198 (DP.D.198-vychislyaemyi-status-vs-harakteristika.md)
- Missing `summary`: DP.D.199 (DP.D.199-rls-politika-vs-zaschita.md)
- Missing `summary`: DP.D.200 (DP.D.200-force-row-level-security.md)
- Missing `summary`: DP.D.201 (DP.D.201-zaschita-kode-flag-test.md)
- Missing `summary`: DP.D.202 (DP.D.202-logicheskoe-gotovo-vs-operacionnoe.md)
- Missing `summary`: DP.D.203 (DP.D.203-icloud-app-container-vs.md)
- Missing `summary`: DP.D.219 (DP.D.219-compression-vs-lazy-razlicheniya.md)
- Missing `summary`: DP.D.220 (DP.D.220-rezhim-otkaza-lazy-komponenta.md)
- Missing `summary`: DP.D.221 (DP.D.221-kachestvennyi-porog-masterstva-vs.md)
- Missing `summary`: DP.D.222 (DP.D.222-non-blocking-audit-rekomendatelnyi.md)
- Missing `summary`: DP.D.223 (DP.D.223-vidimoe-uzkoe-mesto-naglyadnyi.md)
- Missing `summary`: DP.D.224 (DP.D.224-suzhenie-obe-ma-scope.md)
- Missing `summary`: DP.D.225 (DP.D.225-prosrochennyi-shlyuz-blokiruyuschee-preduslovie.md)
- Missing `summary`: DP.D.226 (DP.D.226-uroven-principov-stepen-obschnosti.md)
- Missing `summary`: DP.D.227 (DP.D.227-singular-resurs-0-1.md)
- Missing `summary`: DP.D.228 (DP.D.228-sposob-ispolneniya-kak-pole.md)
- Missing `summary`: DP.D.229 (DP.D.229-zhe-stkii-otkaz-pri.md)
- Missing `summary`: DP.D.230 (DP.D.230-test-gard-validaciyu-obyazan.md)
- Missing `summary`: DP.D.231 (DP.D.231-owned-uzkii-kontrakt-enqueue.md)
- Missing `summary`: DP.D.232 (DP.D.232-edinyi-pisatel-append-only.md)
- Missing `summary`: DP.D.233 (DP.D.233-tihoe-isklyuchenie-pri-deploe.md)
- Missing `summary`: DP.D.234 (DP.D.234-metod-sborki-logika-kompilyacii.md)
- Missing `summary`: DP.D.235 (DP.D.235-sozdanie-artefakta-rukovodstva-edinorazovaya.md)
- Missing `summary`: DP.D.236 (DP.D.236-dostavka-obucheniya-rabochemu-kontekstu.md)
- Missing `summary`: DP.D.237 (DP.D.237-unikalnyi-element-svyazki-vs.md)
- Missing `summary`: DP.D.238 (DP.D.238-smesche-nnyi-proksi-vs.md)
- Missing `summary`: DP.D.239 (DP.D.239-pack-tip-vs-db.md)
- Missing `summary`: DP.D.241 (DP.D.241-reestr-registry-vs-prodovoe.md)
- Missing `summary`: DP.D.242 (DP.D.242-sobytie-perehoda-per-transition.md)
- Missing `summary`: DP.D.243 (DP.D.243-kontur-iwe-vs-fizicheskoe.md)
- Missing `summary`: DP.D.244 (DP.D.244-formalizuemoe-znanie-pack-fpf.md)
- Missing `summary`: DP.D.245 (DP.D.245-vladenie-universalnoe-vs-personalnoe.md)
- Missing `summary`: DP.D.249 (DP.D.249-sc-service-clause-vs.md)
- Missing `summary`: DP.D.250 (DP.D.250-vladelec-domena-arhitekturnymi-polnomochiyami.md)
- Missing `summary`: DP.D.251 (DP.D.251-service-layer-permission-vs-execution-obligation.md)
- Missing `summary`: DP.D.253 (DP.D.253-object-tiering-model.md)
- Missing `summary`: DP.D.257 (DP.D.257-synchronized-file-invariant.md)
- Missing `summary`: DP.D.258 (DP.D.258-paas-vs-saas.md)
- Missing `summary`: DP.D.259 (DP.D.259-scaffold-iniciaciya-vs-point.md)
- Missing `summary`: DP.D.260 (DP.D.260-obnaruzhenie-dei-stvie-kontur.md)
- Missing `summary`: DP.D.261 (DP.D.261-parliament-model-platforma-vs.md)
- Missing `summary`: DP.D.262 (DP.D.262-platform-l2-vs.md)
- Missing `summary`: DP.D.263 (DP.D.263-kontury-l1-l4.md)
- Missing `summary`: DP.D.264 (DP.D.264-method-uroven-roli.md)
- Missing `summary`: DP.D.265 (DP.D.265-kastomizaciya-agenta-harness.md)
- Missing `summary`: DP.D.266 (DP.D.266-bally-gei-mifikaciya.md)
- Missing `summary`: DP.D.267 (DP.D.267-context-engineering-vs.md)
- Missing `summary`: DP.D.268 (DP.D.268-in-memory-session.md)
- Missing `summary`: DP.D.269 (DP.D.269-content-role-vs.md)
- Missing `summary`: DP.D.270 (DP.D.270-kosmeticheskii-bag-vs.md)
- Missing `summary`: DP.D.271 (DP.D.271-pool-acquire-timeout.md)
- Missing `summary`: DP.D.272 (DP.D.272-iwe-brauzere-vs.md)
- Missing `summary`: DP.D.273 (DP.D.273-strategicheskaya-domennaya-rol.md)
- Missing `summary`: DP.D.274 (DP.D.274-negative-definition-vs.md)
- Missing `summary`: DP.D.275 (DP.D.275-golos-voice-vs.md)
- Missing `summary`: DP.D.276 (DP.D.276-doors-vs-lanes-audience-segmentation.md)
- Missing `summary`: DP.D.277 (DP.D.277-p2w-problem-to-work-canonical-term.md)
- Missing `summary`: DP.D.278 (DP.D.278-intermittent-failure-vs-continuous-degradation.md)
- Missing `summary`: DP.D.279 (DP.D.279-severity-scale-vs-finding-id.md)
- Missing `summary`: DP.D.280 (DP.D.280-two-assessments-need-matching-methodology.md)
- Missing `summary`: DP.D.281 (DP.D.281-marker-vs-generator-upstream-downstream.md)
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
- Missing `summary`: DP.ROLE.088 (DP.ROLE.088-router-material-classifier.md)
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
- Missing `summary`: DP.M.302 (DP.M.302-trusted-reference-immutable-audit-table.md)
- Missing `summary`: DP.M.303 (DP.M.303-gated-ddl-separate-from-application-code.md)
- Missing `summary`: DP.M.304 (DP.M.304-local-import-optional-backend.md)
- Missing `summary`: DP.M.305 (DP.M.305-frozen-formula-hash-change-control.md)
- Missing `summary`: DP.M.306 (DP.M.306-honest-tile-degradation-status-codes.md)
- Missing `summary`: DP.M.307 (DP.M.307-bootstrap-mode-sample-size-thresholds.md)
- Missing `summary`: DP.M.308 (DP.M.308-reader-contract-check-before-gate-removal.md)
- Missing `summary`: DP.M.309 (DP.M.309-halliday-language-rule-routing.md)
- Missing `summary`: DP.M.325 (DP.M.325-radar-analog-search-before-build.md)
- Missing `summary`: DP.M.326 (DP.M.326-crystallization-threshold.md)
- Missing `summary`: DP.M.327 (DP.M.327-multi-level-lookup-diagnostic-precision.md)
- Missing `summary`: DP.M.328 (DP.M.328-yaml-preload-pure-bash-lookup.md)
- Missing `summary`: DP.M.329 (DP.M.329-webhook-idempotency-db-constraint.md)
- Missing `summary`: DP.M.341 (DP.M.341-verify-existing-security-pattern-before-implement.md)
- Missing `summary`: DP.M.342 (DP.M.342-grant-execute-not-direct-table-access-via-security-definer.md)
- Missing `summary`: DP.M.343 (DP.M.343-app-user-id-set-local-rls-bridge-shared-role.md)
- Missing `summary`: DP.M.344 (DP.M.344-pre-apply-prod-state-discovery.md)
- Missing `summary`: DP.M.345 (DP.M.345-render-checklist-separation-from-generator.md)
- Missing `summary`: DP.M.348 (DP.M.348-content-first-audit-phase-order.md)
- Missing `summary`: DP.M.349 (DP.M.349-commit-msg-guard-bypass-tags.md)
- Missing `summary`: DP.M.351 (DP.M.351-neon-pgbouncer-advisory-lock-retry-reconnect.md)
- Missing `summary`: DP.M.360 (DP.M.360-server-side-auth-context-enrichment.md)
- Missing `summary`: DP.M.365 (DP.M.365-instruction-file-compression-peer-review.md)
- Missing `summary`: DP.M.371 (DP.M.371-byte-div5-token-proxy.md)
- Missing `summary`: DP.M.372 (DP.M.372-flock-single-pass-lock-granularity.md)
- Missing `summary`: DP.M.373 (DP.M.373-live-reproduction-snapshot-diagnosis.md)
- Missing `summary`: DP.M.374 (DP.M.374-webhook-jwt-identity-provider-auth.md)
- Missing `summary`: DP.M.375 (DP.M.375-bidirectional-git-sync-split-timers.md)
- Missing `summary`: DP.M.376 (DP.M.376-source-scoped-candidate-pool-retrieval.md)
- Missing `summary`: DP.M.377 (DP.M.377-corpus-fanout-positioning-palette.md)
- Missing `summary`: DP.M.378 (DP.M.378-triple-check-public-platform-text.md)
- Missing `summary`: DP.M.379 (DP.M.379-conditional-expensive-agent-step.md)
- Missing `summary`: DP.M.380 (DP.M.380-retry-directive-format-anchor.md)
- Missing `summary`: DP.M.381 (DP.M.381-vdv-dead-output-gap-audit.md)
- Missing `summary`: DP.M.382 (DP.M.382-cross-repo-publish-attributed-squash-commit.md)
- Missing `summary`: DP.M.383 (DP.M.383-onboarding-work-not-learn-frame.md)
- Missing `summary`: DP.M.384 (DP.M.384-close-known-gap-immediately.md)
- Missing `summary`: DP.M.385 (DP.M.385-independent-agent-convergence-confidence.md)
- Missing `summary`: DP.M.386 (DP.M.386-mentor-context-sufficiency-gate.md)
- Missing `summary`: DP.M.387 (DP.M.387-row-digest-gdpr-stable-anchor.md)
- Missing `summary`: DP.M.388 (DP.M.388-key-rename-backward-compat-fallback.md)
- Missing `summary`: DP.M.389 (DP.M.389-oauth-hypothesis-exclusion-diagnostic.md)
- Missing `summary`: DP.M.390 (DP.M.390-keyword-triage-batch-knowledge-extraction.md)
- Missing `summary`: DP.M.391 (DP.M.391-mmem-antithesis-content-hook.md)
- Missing `summary`: DP.M.392 (DP.M.392-pattern-in-action-without-code-name.md)
- Missing `summary`: DP.M.393 (DP.M.393-t0-guard-service-clause-conditional-activation.md)
- Missing `summary`: DP.M.394 (DP.M.394-routing-vocab-two-stage-matching-gate.md)
- Missing `summary`: DP.M.395 (DP.M.395-docker-apple-silicon-build-host.md)
- Missing `summary`: DP.M.396 (DP.M.396-audience-map-driven-communication.md)
- Missing `summary`: DP.M.397 (DP.M.397-terminology-migration-work-not-learn.md)
- Missing `summary`: DP.M.398 (DP.M.398-multiple-db-connection-paths-risk.md)
- Missing `summary`: DP.M.399 (DP.M.399-batch-checkpoint-resume-external-api.md)
- Missing `summary`: DP.M.400 (DP.M.400-bigserial-monotonic-tip-detector.md)
- Missing `summary`: DP.M.401 (DP.M.401-closure-what-move-opened-question.md)
- Missing `summary`: DP.M.402 (DP.M.402-world-research-phase-before-pipeline-design.md)
- Missing `summary`: DP.M.403 (DP.M.403-observable-signal-for-state-transition-gate.md)
- Missing `summary`: DP.M.404 (DP.M.404-explicit-subject-in-agreement-ritual.md)
- Missing `summary`: DP.M.405 (DP.M.405-post-write-self-check-shell-scripts.md)
- Missing `summary`: DP.M.407 (DP.M.407-l4-personal-vs-l2-platform-infra-test.md)
- Missing `summary`: DP.M.409 (DP.M.409-check-adjacent-wp-implementation-at-closure.md)
- Missing `summary`: DP.METHOD.051 (DP.METHOD.051-n8n-builtin-healthz.md)
- Missing `summary`: DP.METHOD.059 (DP.METHOD.059-bash-32-portability-python3-heredoc.md)
- Missing `summary`: DP.METHOD.060 (DP.METHOD.060-skill-promotion-l2-to-l1.md)
- Missing `summary`: DP.METHOD.061 (DP.METHOD.061-incremental-architecture-seed-order.md)
- Missing `summary`: DP.METHOD.062 (DP.METHOD.062-skill-description-scope-guard.md)
- Missing `summary`: DP.METHOD.103 (DP.METHOD.103-language-parametric-onboarding-route.md)
- Missing `summary`: DP.METHOD.104 (DP.METHOD.104-lpf-role-substitution-test.md)
- Missing `summary`: DP.METHOD.105 (DP.METHOD.105-rule-by-function-not-location.md)
- Missing `summary`: DP.METHOD.106 (DP.METHOD.106-mutation-test-honesty-check.md)
- Missing `summary`: DP.METHOD.107 (DP.METHOD.107-rule-by-template-structure.md)
- Missing `summary`: DP.METHOD.108 (DP.METHOD.108-error-counter-scale-diagnostic.md)
- Missing `summary`: DP.METHOD.109 (DP.METHOD.109-measurement-layer-check-before-data-wait.md)
- Missing `summary`: DP.METHOD.110 (DP.METHOD.110-alert-repeat-ack-gate.md)
- Missing `summary`: DP.METHOD.111 (DP.METHOD.111-date-failure-from-first-launch-log-boundary.md)
- Missing `summary`: DP.METHOD.112 (DP.METHOD.112-silent-component-triage-necessity-first.md)
- Missing `summary`: DP.METHOD.113 (DP.METHOD.113-acl-companion-artifact-schema-pipeline.md)
- Missing `summary`: DP.METHOD.114 (DP.METHOD.114-diagnostics-on-transient-failure.md)
- Missing `summary`: DP.METHOD.115 (DP.METHOD.115-storage-writer-diagnosis-via-grants.md)
- Missing `summary`: DP.METHOD.118 (DP.METHOD.118-peer-dispute-first-source-verification.md)
- Missing `summary`: DP.METHOD.119 (DP.METHOD.119-watchdog-check-guard-order.md)
- Missing `summary`: DP.METHOD.120 (DP.METHOD.120-multi-session-reconcile-gate.md)
- Missing `summary`: DP.METHOD.122 (DP.METHOD.122-month-close-rebuild-strategic-context.md)
- Missing `summary`: DP.METHOD.123 (DP.METHOD.123-migration-number-collision-as-coordination-signal.md)
- Missing `summary`: DP.METHOD.124 (DP.METHOD.124-stateless-windowed-recompute.md)
- Missing `summary`: DP.METHOD.125 (DP.METHOD.125-guard-normalized-ratio-not-raw-numerator.md)
- Missing `summary`: DP.METHOD.126 (DP.METHOD.126-context-freshness-flag.md)
- Missing `summary`: DP.METHOD.127 (DP.METHOD.127-wp-next-step-guide-block.md)
- Missing `summary`: DP.METHOD.128 (DP.METHOD.128-detector-selftest-synthetic-regression.md)
- Missing `summary`: DP.METHOD.129 (DP.METHOD.129-quarterly-cadence-month-close-mod3.md)
- Missing `summary`: DP.METHOD.130 (DP.METHOD.130-atomic-upsert-on-conflict-race-prevention.md)
- Missing `summary`: DP.METHOD.134 (DP.METHOD.134-authored-file-deferred-conflict-delivery.md)
- Missing `summary`: DP.METHOD.135 (DP.METHOD.135-render-checklist-separate-artifact.md)
- Missing `summary`: DP.METHOD.136 (DP.METHOD.136-archive-integrity-listing-baseline.md)
- Missing `summary`: DP.METHOD.137 (DP.METHOD.137-staged-migration-read-path-deferred-delete.md)
- Missing `summary`: DP.METHOD.138 (DP.METHOD.138-knowledge-atom-normal-form-multi-consumer.md)
- Missing `summary`: DP.METHOD.139 (DP.METHOD.139-methodology-pilot-one-document-before-corpus.md)
- Missing `summary`: DP.METHOD.140 (DP.METHOD.140-e2e-pipeline-shakedown.md)
- Missing `summary`: DP.METHOD.141 (DP.METHOD.141-sota-sheet-lite-before-pack-name.md)
- Missing `summary`: DP.METHOD.142 (DP.METHOD.142-single-registry-entity-type.md)
- Missing `summary`: DP.METHOD.143 (DP.METHOD.143-non-fatal-exit-code-explicit-tolerate.md)
- Missing `summary`: DP.METHOD.144 (DP.METHOD.144-feature-flag-dual-write-cutover.md)
- Missing `summary`: DP.METHOD.159 (DP.METHOD.159-auto-detect-one-question-ambiguity.md)
- Missing `summary`: DP.METHOD.160 (DP.METHOD.160-additive-router-new-axis-no-regression.md)
- Missing `summary`: DP.METHOD.161 (DP.METHOD.161-close-writes-tomorrow-inputs.md)
- Missing `summary`: DP.METHOD.167 (DP.METHOD.167-memory-index-hub-collapse.md)
- Missing `summary`: DP.METHOD.168 (DP.METHOD.168-residency-gate-data-consent.md)
- Missing `summary`: DP.METHOD.169 (DP.METHOD.169-corpus-dedup-prefilter-enumerate-passes.md)
- Missing `summary`: DP.METHOD.170 (DP.METHOD.170-three-layer-data-residency.md)
- Missing `summary`: DP.METHOD.172 (DP.METHOD.172-en-distribution-self-contained-verification.md)
- Missing `summary`: DP.METHOD.173 (DP.METHOD.173-context-isolated-verifier.md)
- Missing `summary`: DP.METHOD.174 (DP.METHOD.174-fail-closed-escape-hatch-explicit-phrase.md)
- Missing `summary`: DP.METHOD.175 (DP.METHOD.175-coupled-db-changes-observable-gap.md)
- Missing `summary`: DP.METHOD.176 (DP.METHOD.176-n-parallel-adversarial-verifiers-quality-gate.md)
- Missing `summary`: DP.METHOD.177 (DP.METHOD.177-sequential-verification-rounds.md)
- Missing `summary`: DP.METHOD.178 (DP.METHOD.178-user-facing-tool-closure-criterion.md)
- Missing `summary`: DP.METHOD.179 (DP.METHOD.179-pack-creation-sota-check-step-1-5.md)
- Missing `summary`: DP.METHOD.180 (DP.METHOD.180-pre-promotion-peer-gate.md)
- Missing `summary`: DP.METHOD.181 (DP.METHOD.181-three-state-consistency-verdict.md)
- Missing `summary`: DP.METHOD.182 (DP.METHOD.182-commit-time-hook-shift-left.md)
- Missing `summary`: DP.METHOD.183 (DP.METHOD.183-cache-miss-rate-llm-observability.md)
- Missing `summary`: DP.METHOD.184 (DP.METHOD.184-optional-executable-hook-l1-l3.md)
- Missing `summary`: DP.METHOD.185 (DP.METHOD.185-subsection-over-standalone-command.md)
- Missing `summary`: DP.METHOD.186 (DP.METHOD.186-fsm-read-model-permission-alternative.md)
- Missing `summary`: DP.METHOD.188 (DP.METHOD.188-registry-current-vs-target-tier-three-fields.md)
- Missing `summary`: DP.METHOD.189 (DP.METHOD.189-operator-first-rollout-deployment-safety.md)
- Missing `summary`: DP.METHOD.190 (DP.METHOD.190-retry-once-then-alert-quality-regression.md)
- Missing `summary`: DP.METHOD.191 (DP.METHOD.191-cherry-pick-recovery-parallel-agent-branch-switch.md)
- Missing `summary`: DP.METHOD.194 (DP.METHOD.194-r30-assembly-distinctions-source.md)
- Missing `summary`: DP.METHOD.195 (DP.METHOD.195-apply-captures-pack-registration-trigger.md)
- Missing `summary`: DP.METHOD.196 (DP.METHOD.196-archgate-incident-history-over-general-rule.md)
- Missing `summary`: DP.METHOD.197 (DP.METHOD.197-archgate-operator-regression-over-purity.md)
- Missing `summary`: DP.METHOD.198 (DP.METHOD.198-dual-writer-disjoint-id-ranges.md)
- Missing `summary`: DP.METHOD.199 (DP.METHOD.199-migration-smoke-under-real-role.md)
- Missing `summary`: DP.METHOD.205 (DP.METHOD.205-acceptance-sampling-llm-batch.md)
- Missing `summary`: DP.METHOD.206 (DP.METHOD.206-nullable-draft-path-intent-vs-artifact.md)
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
- Missing `summary`: DP.FM.131 (DP.FM.131-incomplete-lifecycle-tooling-registry-rot.md)
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
- Missing `summary`: DP.FM.152 (DP.FM.152-tracked-dir-added-to-gitignore.md)
- Missing `summary`: DP.FM.153 (DP.FM.153-intermittent-401-static-key-proxy-or-env.md)
- Missing `summary`: DP.FM.154 (DP.FM.154-commit-without-push-deferred-divergence.md)
- Missing `summary`: DP.FM.155 (DP.FM.155-cross-db-trigger-boundary.md)
- Missing `summary`: DP.FM.161 (DP.FM.161-pack-event-name-drift.md)
- Missing `summary`: DP.FM.162 (DP.FM.162-wp-context-vapor-claim-drift.md)
- Missing `summary`: DP.FM.163 (DP.FM.163-npm-ci-vs-npm-run-lock-mismatch.md)
- Missing `summary`: DP.FM.164 (DP.FM.164-jose-jwks-dns-failure-timeout.md)
- Missing `summary`: DP.FM.165 (DP.FM.165-foreground-shell-orphan-ide-extension.md)
- Missing `summary`: DP.FM.166 (DP.FM.166-schema-consumer-contract-breach.md)
- Missing `summary`: DP.FM.167 (DP.FM.167-silent-false-disables-except-fallback.md)
- Missing `summary`: DP.FM.168 (DP.FM.168-metric-zero-active-user-identity-first.md)
- Missing `summary`: DP.FM.169 (DP.FM.169-silent-fallback-content-pipeline.md)
- Missing `summary`: DP.FM.171 (DP.FM.171-ui-visibility-vs-code-access.md)
- Missing `summary`: DP.FM.186 (DP.FM.186-append-only-phantom-early-writer.md)
- Missing `summary`: DP.FM.187 (DP.FM.187-raw-template-execution-silent-artifacts.md)
- Missing `summary`: DP.FM.188 (DP.FM.188-shared-db-owner-nonattribution.md)
- Missing `summary`: DP.FM.189 (DP.FM.189-hash-without-prev-chain-false-immutability.md)
- Missing `summary`: DP.FM.190 (DP.FM.190-validator-no-enforcement-point.md)
- Missing `summary`: DP.FM.192 (DP.FM.192-subshell-redirect-silences-exit-code.md)
- Missing `summary`: DP.FM.193 (DP.FM.193-git-dead-hook-core-hookspath.md)
- Missing `summary`: DP.FM.194 (DP.FM.194-launchd-stale-pid-port-occupation.md)
- Missing `summary`: DP.FM.195 (DP.FM.195-retroactive-history-cleanup-data-falsification.md)
- Missing `summary`: DP.FM.196 (DP.FM.196-deferred-sql-in-auto-executed-migration-file.md)
- Missing `summary`: DP.FM.197 (DP.FM.197-replay-tool-misidentified-as-incoming-buffer.md)
- Missing `summary`: DP.FM.198 (DP.FM.198-crypto-shredding-not-gdpr-erasure.md)
- Missing `summary`: DP.FM.199 (DP.FM.199-role-revoke-schema-owner-bypass.md)
- Missing `summary`: DP.FM.200 (DP.FM.200-audit-log-missing-source-service.md)
- Missing `summary`: DP.FM.201 (DP.FM.201-bsd-gnu-sed-ampersand-escaping.md)
- Missing `summary`: DP.FM.202 (DP.FM.202-multiple-registries-one-entity-drift.md)
- Missing `summary`: DP.FM.203 (DP.FM.203-deployed-consensus-not-final-verification.md)
- Missing `summary`: DP.FM.204 (DP.FM.204-multi-row-insert-forks-trigger-chain.md)
- Missing `summary`: DP.FM.205 (DP.FM.205-fsm-intermediate-state-without-exit-path.md)
- Missing `summary`: DP.FM.206 (DP.FM.206-ddl-in-ensure-schema-locks-every-run.md)
- Missing `summary`: DP.FM.207 (DP.FM.207-grep-keyword-not-anchored-to-header-false-green.md)
- Missing `summary`: DP.FM.208 (DP.FM.208-bash32-ifs-tab-nosplit.md)
- Missing `summary`: DP.FM.209 (DP.FM.209-sql-injection-fstring-parameter.md)
- Missing `summary`: DP.FM.210 (DP.FM.210-zsh-bsd-grep-multiline-false-green.md)
- Missing `summary`: DP.FM.211 (DP.FM.211-gitignore-env-pattern-incomplete.md)
- Missing `summary`: DP.FM.212 (DP.FM.212-filter-branch-all-destroys-stash.md)
- Missing `summary`: DP.FM.213 (DP.FM.213-filter-branch-worktree-sync.md)
- Missing `summary`: DP.FM.214 (DP.FM.214-zsh-word-split-bsd-grep-multiline-false-green.md)
- Missing `summary`: DP.FM.215 (DP.FM.215-semaphore-agent-id-race-parallel-sessions.md)
- Missing `summary`: DP.FM.216 (DP.FM.216-multi-owner-aggregate-policy-granularity.md)
- Missing `summary`: DP.FM.217 (DP.FM.217-shell-pid-not-agent-session-pid.md)
- Missing `summary`: DP.FM.218 (DP.FM.218-wrong-diagnosis-hides-real-bug.md)
- Missing `summary`: DP.FM.219 (DP.FM.219-type-cast-in-where-breaks-index.md)
- Missing `summary`: DP.FM.220 (DP.FM.220-health-check-ddl-side-effect-false-fail.md)
- Missing `summary`: DP.FM.221 (DP.FM.221-timezone-msk-utc-date-comparison-false-nudge.md)
- Missing `summary`: DP.FM.222 (DP.FM.222-verbal-permission-not-process-env.md)
- Missing `summary`: DP.FM.223 (DP.FM.223-verifier-hallucinated-verdict-zero-tool-calls.md)
- Missing `summary`: DP.FM.224 (DP.FM.224-smart-sync-stub-exists-not-local.md)
- Missing `summary`: DP.FM.225 (DP.FM.225-rsync-delete-empty-delta-wipe.md)
- Missing `summary`: DP.FM.226 (DP.FM.226-git-worktree-detached-head-push-fail.md)
- Missing `summary`: DP.FM.227 (DP.FM.227-bash-set-e-function-wrapper-exit-status.md)
- Missing `summary`: DP.FM.228 (DP.FM.228-railway-liveness-probe-auth-blocks.md)
- Missing `summary`: DP.FM.229 (DP.FM.229-llm-aggregator-single-key-spof.md)
- Missing `summary`: DP.FM.230 (DP.FM.230-return-instead-of-raise-masks-exit-zero.md)
- Missing `summary`: DP.FM.231 (DP.FM.231-ambiguous-param-name-dual-axis-silent-shift.md)
- Missing `summary`: DP.FM.232 (DP.FM.232-phantom-field-prototype-seam.md)
- Missing `summary`: DP.FM.233 (DP.FM.233-append-log-schema-version-not-bumped.md)
- Missing `summary`: DP.FM.235 (DP.FM.235-eager-framework-context-bloat.md)
- Missing `summary`: DP.FM.236 (DP.FM.236-premature-cache-invalidation.md)
- Missing `summary`: DP.FM.237 (DP.FM.237-provenance-state-outside-repo.md)
- Missing `summary`: DP.FM.238 (DP.FM.238-self-referential-exemption.md)
- Missing `summary`: DP.FM.239 (DP.FM.239-arch-decision-unversioned.md)
- Missing `summary`: DP.FM.240 (DP.FM.240-rollback-single-param-leak.md)
- Missing `summary`: DP.FM.241 (DP.FM.241-polysemous-term-satellite-bug.md)
- Missing `summary`: DP.FM.242 (DP.FM.242-false-analogy-forced-constraint.md)
- Missing `summary`: DP.FM.243 (DP.FM.243-deterministic-calc-delegated-to-llm.md)
- Missing `summary`: DP.FM.244 (DP.FM.244-staged-delete-cross-agent-commit.md)
- Missing `summary`: DP.FM.245 (DP.FM.245-inline-comment-not-literal-validator-bypass.md)
- Missing `summary`: DP.FM.247 (DP.FM.247-payment-api-ambiguous-terminal-status.md)
- Missing `summary`: DP.FM.248 (DP.FM.248-metric-threshold-unit-rename.md)
- Missing `summary`: DP.FM.249 (DP.FM.249-stale-env-var-default-renamed-repo.md)
- Missing `summary`: DP.FM.250 (DP.FM.250-fetch-head-parallel-git-pull-race.md)
- Missing `summary`: DP.FM.251 (DP.FM.251-onbootsec-timer-gap-collapse.md)
- Missing `summary`: DP.FM.252 (DP.FM.252-global-pull-rebase-overrides-ff-only.md)
- Missing `summary`: DP.FM.253 (DP.FM.253-legacy-read-after-catalog-merge.md)
- Missing `summary`: DP.FM.254 (DP.FM.254-llm-silent-truncation-finish-reason.md)
- Missing `summary`: DP.FM.255 (DP.FM.255-transitive-shim-dependency-ci-fail.md)
- Missing `summary`: DP.FM.256 (DP.FM.256-conditional-llm-cleanup-markup-leak.md)
- Missing `summary`: DP.FM.257 (DP.FM.257-railway-project-token-wrong-auth-header.md)
- Missing `summary`: DP.FM.258 (DP.FM.258-parallel-agent-branch-switch-shared-checkout.md)
- Missing `summary`: DP.FM.259 (DP.FM.259-measurement-gaming-form-without-substance.md)
- Missing `summary`: DP.FM.260 (DP.FM.260-check-infrastructure-path-resolution-masked-as-logic-error.md)
- Missing `summary`: DP.FM.262 (DP.FM.262-shared-error-db-env-leak.md)
- Missing `summary`: DP.FM.263 (DP.FM.263-git-copy-instead-of-move-cross-repo.md)
- Missing `summary`: DP.FM.264 (DP.FM.264-housekeeping-semaphore-missing-slug.md)
- Missing `summary`: DP.FM.265 (DP.FM.265-catch-all-exception-masks-real-errors.md)
- Missing `summary`: DP.FM.266 (DP.FM.266-exit-code-not-structural-correctness.md)
- Missing `summary`: DP.FM.267 (DP.FM.267-publish-job-in-mirror-not-source.md)
- Missing `summary`: DP.FM.268 (DP.FM.268-checkout-persist-credentials-overrides-app-token.md)
- Missing `summary`: DP.FM.269 (DP.FM.269-time-window-scope-gate-parallel-agents.md)
- Missing `summary`: DP.FM.270 (DP.FM.270-bootstrap-migration-drift-multi-version.md)
- Missing `summary`: DP.FM.271 (DP.FM.271-stale-repair-overwrites-user-owned-files.md)
- Missing `summary`: DP.FM.272 (DP.FM.272-mtime-scope-gate-fails-for-headless-ops.md)
- Missing `summary`: DP.FM.273 (DP.FM.273-github-list-api-silent-truncation.md)
- Missing `summary`: DP.FM.274 (DP.FM.274-psql-heredoc-silent-sql-error.md)
- Missing `summary`: DP.FM.275 (DP.FM.275-serverless-db-first-request-transient-fail.md)
- Missing `summary`: DP.FM.276 (DP.FM.276-llm-output-regex-punctuation-variance.md)
- Missing `summary`: DP.FM.277 (DP.FM.277-dispatch-key-structural-tag-vs-content.md)
- Missing `summary`: DP.FM.278 (DP.FM.278-cloudflare-ai-workers-http500-transient.md)
- Missing `summary`: DP.FM.279 (DP.FM.279-day-open-hook-transitive-call.md)
- Missing `summary`: DP.FM.280 (DP.FM.280-cross-db-staging-tables-must-exist-on-both-ends.md)
- Missing `summary`: DP.FM.287 (DP.FM.287-parallel-agent-pack-authoring-drift.md)
- Missing `summary`: DP.FM.288 (DP.FM.288-shell-gate-zero-iteration-silent-pass.md)
- Missing `summary`: DP.FM.289 (DP.FM.289-sqlite-date-null-nonstandard-tz-offset.md)
- Missing `summary`: DP.FM.290 (DP.FM.290-apple-health-multiple-rows-per-date.md)
- Missing `summary`: DP.FM.291 (DP.FM.291-prompt-not-migrated-to-service-repo.md)
- Missing `summary`: DP.FM.292 (DP.FM.292-jwt-single-flag-n-products-breakdown.md)
- Missing `summary`: DP.FM.296 (DP.FM.296-routing-vocab-stale-path-ke-silent-defer.md)
- Missing `summary`: DP.FM.297 (DP.FM.297-denormalized-header-drifts-from-body.md)
- Missing `summary`: DP.FM.298 (DP.FM.298-lifecycle-script-stale-glob-pattern-silent-noop.md)
- Missing `summary`: DP.FM.299 (DP.FM.299-verifier-criterion-missing-addressed-path.md)
- Missing `summary`: DP.FM.300 (DP.FM.300-retry-handler-exception-swallowed.md)
- Missing `summary`: DP.FM.334 (DP.FM.334-network-hang-in-loop.md)
- Missing `summary`: DP.FM.335 (DP.FM.335-install-script-unconditional-config-overwrite.md)
- Missing `summary`: DP.FM.336 (DP.FM.336-resource-picker-recency-without-active-status.md)
- Missing `summary`: DP.FM.337 (DP.FM.337-archgate-new-feature-regression-blind-spot.md)
- Missing `summary`: DP.FM.339 (DP.FM.339-diff-aware-guard-indirect-bypass-blind.md)
- Missing `summary`: DP.FM.340 (DP.FM.340-cross-repo-import-missing-checkout-guard.md)
- Missing `summary`: DP.FM.341 (DP.FM.341-consent-gate-return-value-discarded.md)
- Missing `summary`: DP.FM.342 (DP.FM.342-exact-token-replace-H1-only.md)
- Missing `summary`: DP.FM.346 (DP.FM.346-registry-strikethrough-parsed-as-pending.md)
- Missing `summary`: DP.FM.347 (DP.FM.347-partial-fix-same-bug-class-leaves-gaps.md)
- Missing `summary`: DP.FM.348 (DP.FM.348-re-sub-unescaped-replacement-crash.md)
- Missing `summary`: DP.FM.349 (DP.FM.349-duplicate-field-value-silent-ambiguous-selection.md)
- Missing `summary`: DP.FM.350 (DP.FM.350-non-transactional-delete-insert-data-loss.md)
- Missing `summary`: DP.FM.351 (DP.FM.351-on-conflict-nothing-breaks-parent-chain.md)
- Missing `summary`: DP.FM.352 (DP.FM.352-live-session-races-headless-for-oauth-token.md)
- Missing `summary`: DP.FM.353 (DP.FM.353-wrong-env-var-path-masked-by-local-copy.md)
- Missing `summary`: DP.FM.354 (DP.FM.354-hash-comparison-fails-3way-merge-without-base.md)
- Missing `summary`: DP.FM.355 (DP.FM.355-moonshot-direct-key-dead-oauth-fallback.md)
- Missing `summary`: DP.SOTA.029 (DP.SOTA.029-ai-era-two-crisis-groups.md)
- Missing `summary`: DP.SOTA.030 (DP.SOTA.030-eam-agent-manifest-standard.md)
- Missing `summary`: DP.SOTA.031 (DP.SOTA.031-async-factory-deterministic-pipeline.md)
- Missing `summary`: DP.SOTA.032 (DP.SOTA.032-semantic-chunking-rag.md)
- Missing `summary`: DP.SOTA.033 (DP.SOTA.033-ai-learning-platform-commoditization-2026.md)
- Missing `summary`: DP.SOTA.034 (DP.SOTA.034-bigtech-context-commoditization.md)
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
- Missing `summary`: DP.SC.054 (DP.SC.054-process-runner.md)
- Missing `summary`: DP.SC.131 (DP.SC.131-backup-process.md)
- Missing `summary`: DP.SC.140 (DP.SC.140-club-action-catalog.md)
- Missing `summary`: DP.SC.145 (DP.SC.145-llm-router.md)
- Missing `summary`: DP.SC.146 (DP.SC.146-secret-drift-detector.md)
- Missing `summary`: DP.SC.167 (DP.SC.167-hermes-chat.md)
- Missing `summary`: DP.SC.168 (DP.SC.168-onboarding.md)
- Missing `summary`: DP.SC.169 (DP.SC.169-conductor-lite.md)
- Missing `summary`: DP.SC.170 (DP.SC.170-onboarder.md)
- Missing `summary`: DP.SC.171 (DP.SC.171-conveyor-development.md)
- Missing `summary`: DP.SC.180 (DP.SC.180-unit-economics.md)
- Missing `summary`: DP.SC.183 (DP.SC.183-bot-llm-dialog.md)
- Missing `summary`: DP.SC.184 (DP.SC.184-bot-day-open.md)
- Missing `summary`: DP.SC.186 (DP.SC.186-bot-agent-session.md)
- Missing `summary`: DP.SC.187 (DP.SC.187-local-gateway-render.md)
- Missing `summary`: DP.SC.191 (DP.SC.191-capacity-commitment-decompose-load-type.md)
- Missing `summary`: DP.SC.192 (DP.SC.192-scheduled-wp-launch.md)
- Missing `summary`: DP.SC.193 (DP.SC.193-ontological-specificity-lens.md)
- Missing `summary`: DP.SC.195 (DP.SC.195-drr-pack-replenishment-gate.md)
- Missing `summary`: DP.SC.196 (DP.SC.196-content-cleanup-signal-registry.md)

## Staleness Warnings (>90 days since update)

| ID | Days Since Update |
|----|-------------------|
| DP.FM.008 | 124 |
| DP.FM.009 | 104 |
| DP.FM.011 | 103 |
| DP.FM.012 | 102 |
| DP.FM.010 | 97 |

---

*Generated by `scripts/generate-map.py` on 2026-07-23*