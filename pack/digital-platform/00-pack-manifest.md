Domain: DP
Pack directory: /Users/tserentserenov/IWE/PACK-digital-platform/pack/digital-platform
MAP written to: /Users/tserentserenov/IWE/PACK-digital-platform/pack/digital-platform/07-map/DP.MAP.001.md
Manifest updated: /Users/tserentserenov/IWE/PACK-digital-platform/pack/digital-platform/00-pack-manifest.md
## Entity Index

| ID | Name | Kind | Summary | Status |
|----|------|------|---------|--------|
| DP.AISYS.008 | ДЗ-чекер | AISYS | ИИ-система автоматической проверки домашних заданий учеников по нормативам из руководств | active |
| DP.AISYS.013 | Знание-Экстрактор | AISYS | Prompt-based ИИ-система экстракции знаний из сессий в Pack-совместимые сущности и DS docs/ через двойной routing | draft |
| DP.AISYS.014 | AIST Bot | AISYS | Telegram-бот экосистемы: тонкий клиент с сервисным реестром, ИИ-консультантом, биллингом и связью с цифровым двойником | draft |
| DP.AISYS.015 | Анализатор проговаривания и письма | AISYS | ИИ-система анализа текста/речи на предмет использования понятий, выявления мемов и обновления модели мастерства ученика | active |
| DP.ARCH.001 | Архитектура платформы | ARCH | 3-слойная архитектура ИТ-платформы с 7 характеристиками (ЭМОГССБ) и 25 принципами: эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность, безопасность | active |
| DP.ARCH.002 | Тиры платформы | ARCH | 4 оси полномочий: T0–T4 (учащийся) + TM1–TM3 (наставник) + TA1–TA4 (администратор) + TD1 (разработчик). Каждый тир — конфигурация среды по 5 измерениям. Оси полномочий ортогональны: один человек = T + TM? + TA? + TD?. Отдельно — 2 оси онбординга (оснащение × развитие), см. §2б. | draft |
| DP.ARCH.003 | Архитектура Digital Twin — единая точка расчёта и чтения | ARCH | 8 принципов разделения Calculator / Reader. Единственный калькулятор — R28 Profiler. Интерфейсы — stateless витрины. Каждая цифра трассируется к IND-коду метамодели. | active |
| DP.ARCH.004 | Архитектура данных Neon (Database-per-BoundedContext) | ARCH | 12 баз данных Neon по принципу database-per-BoundedContext. Сводная таблица, карта, ERD по каждой БД, связи, потоки, реестр физ.объектов с маркерами О/С/Р/К, revenue-sharing механика (контракты/сплиты/выплаты), points-ledger (event-sourcing) + эмиссионный отчёт, верификация по чеклистам SPF.SPEC.005, замечаниям Андрея Д1-Д12 и категориям WP-257. | active |
| DP.ARCH.005 | Персона (декларативная модель созидателя) | ARCH | Персона — distributed-entity, представляющая одного носителя (человека) в IWE. Composition: identity-anchor (Ory subject_id или Pre-Grant claim_token) + declarations (Git PACK-personal/DS-my-strategy/captures) + refs (Neon persona_grants). Писатель деклараций = пользователь (или агент по его поручению с acceptance); identity-anchor издаётся системой регистрации. Заменяет часть монолита ЦД (DP.ARCH.003). v1.1 (2026-05-31): добавлен §0 — Носитель ≠ Персона ≠ Декларация + lifecycle anchor. | approved |
| DP.ARCH.006 | Память (Observed события + Derived агрегаты) | ARCH | Память — операционный слой модели пользователя. Писатель = платформа runtime, владелец = Neon. Два под-слоя: Observed (append-only события) + Derived (вычисляемые агрегаты, бывший узкий ЦД). Event Sourcing + CQRS. BKT, HLR, engagement, misconceptions, qualifications. Замещает основную часть монолита DP.ARCH.003. | approved |
| DP.ARCH.007 | Проекция (runtime-компиляция под потребителя) | ARCH | Проекция — эфемерный runtime-слой: агент на лету собирает из Памяти и Персоны ответ под одного потребителя (LLM-промпт, пользовательский view, nudge). Writer = агент в runtime. Owner = нет (не хранится дольше одного вызова). Заменяет часть монолита ЦД (DP.ARCH.003) — §7 Views + §17 Nudge Engine. | approved |
| DP.ARCH.008 | Enforcement требует наблюдателя вне субъекта | ARCH | Архитектурный принцип реализации правил агента: правило, которое проверяется самим агентом по памяти, имеет нулевую силу. Наблюдатель должен находиться ВНЕ субъекта, действия которого он контролирует. Шкала сил: memory (0) → hook (средняя) → deterministic generation (максимальная). | approved |
| DP.ARCH.009-decisions | Decisions | ARCH | — | active |
| DP.ASSIST.001 | ИИ-ассистенты (superseded) | ASSIST | Объединены с DP.ROLE.001 — различие агент/ассистент сохранено как характеристика | superseded |
| DP.CONCEPT.001 | Концепция платформы | CONCEPT | Концепция ИТ-платформы экосистемы: цифровой двойник, ИИ-системы, интеграции, отчуждаемость | active |
| DP.CONCEPT.003 | Адаптивная персонализация | CONCEPT | Принцип и механизм платформы: адаптируется под человека через три слоя — персонализацию, индивидуализацию и адаптивность | active |
| DP.CONCEPT.004 | Three Layers Ai Work | CONCEPT | 3 слоя работы с ИИ: разовый запрос (нет контекста) → роль и инструкция (постоянный системный контекст) → накопленная среда (история решений, документы, проекты). Переход между слоями определяется объёмом переданного контекста, а не моделью или промпт-техникой | draft |
| DP.D.025 | Harness ≠ Agent | D | Harness (упряжь/обвязка) определяет результат больше, чем мощность агента/модели | active |
| DP.D.027 | Content Budget Model (3 оси) | D | Длина, глубина и персонализация контента — три независимые оси, управляемые раздельно | active |
| DP.D.028 | User Data Tiers — тирование данных пользователя | D | Данные пользователя растут с тиром платформы: T0 без Ory (telegram_id) → T1 с Ory (UUID) → T2 (активная подписка) профиль + история + универсальные руководства (одинаковые для всех) → T3 (подключён любой AI-клиент: claude.ai / Claude Code / VS Code / Telegram) персональные артефакты — персональное руководство (WP-149) + Гермес знает историю (Память.Derived) → T4 (+ GitHub) личный Pack + ИИ-агенты (со-мыслитель). T3-условие = AI-клиент подключён, НЕ «ЦД заполнен» (устаревшее, см. DP.D.052; семантика T3 — консенсус WP-406 Ф13). Ортогональные оси: TM (наставник), TA (администратор), TD (разработчик) | active |
| DP.D.029 | Language Model ≠ World Model | D | LLM = пассивные знания о мире из текстов (кабинетный учёный). World Model = активная модель, обновляемая из взаимодействия с реальностью (инженер). Критерий: замыкает ли система цикл действие-измерение-обновление | active |
| DP.D.030 | Топология деплоя платформы | D | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.031 | MCP Access Model: публичный vs приватный | D | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.032 | Единый Circuit Breaker для внешних зависимостей | D | Реализационное решение. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.D.033 | Role-Centric Architecture (Ролецентричная архитектура) | D | Роль описывается независимо от исполнителя. Исполнитель выбирается и подготавливается отдельно. Роль = маска, которую надевает система (сама — если агент, или по воле другого агента — если инструмент). Одно имя (например, 'Синхронизатор') может обозначать и роль, и систему-исполнителя — это разные ракурсы, не тождество. | active |
| DP.D.034 | Three-Axis Access Control Model (Трёхосевая модель доступов) | D | Доступ на платформе определяется тремя ортогональными осями: Entitlement (тир — что доступно по подписке), Role (роль — что можно делать), Scope (область видимости — над чем). Permission = Entitlement × Role × Scope. Устраняет необходимость в подролях (Администратор-1, Администратор-2) — это одна роль с разным scope. | active |
| DP.D.035 | Data Policy — политика данных IWE | D | Единая политика данных платформы: что собирается, где хранится, кому доступно, как удалить. Принятие — при установке шаблона (setup.sh). Агрегирует DP.D.028, DP.D.031, DP.ARCH.005, DP.ARCH.006, DP.ARCH.007 | active |
| DP.D.036 | BYOB Knowledge Architecture | D | Различение BYOB (Bring Your Own Backend) vs Managed: данные пользователя хранятся на его ресурсах, платформа даёт код и L2-знания. Связано с MCP Hub (ADR-018 v2) и контурами L2/L4. | draft |
| DP.D.037 | Рабочий продукт как инструмент связи | D | РП — не красиво оформленные данные, а инструмент, показывающий связь между элементами и работающий на достижение миссии | active |
| DP.D.040 | Мировоззрение → Pack: аналогия художника | D | Художник кодирует мировоззрение в произведение. Профессионал кодирует доменное знание в Pack. Оба трансформируют внутреннее в описание | active |
| DP.D.050 | Роли Созидателя | D | 5 ролей Созидателя (Ученик, Интеллектуал, Профессионал, Исследователь, Просветитель). Каждый человек выполняет все 5 одновременно. Внутри каждой роли — ступени мастерства. Основа траектории персонального развития. | active |
| DP.D.052 | Различение: Персона / Память / Контекст | D | Три слоя пользовательской модели — замена legacy-термина «ЦД». Критерий разделения = writer + owner (source-of-truth), не когнитивный и не по TTL. Персона = distributed-entity (identity-anchor + Git declarations + Neon refs), Память = platform-owned Neon, Контекст (= Проекция) = runtime-ephemeral. v2: разделены оси Writer / Identity-anchor / State-storage / Snapshot-unit (вместо склейки Owner+Артефакт); добавлено различение Носитель ≠ Персона ≠ Декларация Персоны (§10). | active |
| DP.D.053 | Problem Task Workflow | D | — | active |
| DP.D.054 | Dashboard Audience Projections | D | — | active |
| DP.D.055 | Домен vs Тема | D | — | active |
| DP.D.056 | IWE Слои и портируемость | D | — | active |
| DP.D.057 | Routing-решение ≠ Обновление карты маршрутизации | D | — | active |
| DP.D.058 | Service Clause (Обещание) ≠ Carrier (Носитель реализации) | D | — | active |
| DP.D.059 | Три класса хранения credentials при ротации | D | — | active |
| DP.D.060 | Entity-БД vs Special-БД: изолированный threat model и независимый lifecycle | D | — | active |
| DP.D.061 | Neon Db Count Layers | D | — | active |
| DP.D.062 | Потребитель SC — роль, не канал | D | — | active |
| DP.D.063 | Платформа-инициированные vs потребитель-инициированные уведомления | D | — | active |
| DP.D.064 | То же обещание ≠ Другое обещание (scope-дискриминатор при закрытии РП) | D | — | active |
| DP.D.065 | Ortho-различение: специализация-по-содержимому ≠ атрибут-применимый-к-любому | D | — | active |
| DP.D.066 | Чертёж (планирующий артефакт) ≠ Стройка (реализационный артефакт) | D | — | active |
| DP.D.067 | Card ≠ Append-only Event (Aggregate-card vs Event-stream в event sourcing) | D | — | active |
| DP.D.068 | Discovered-WP vs Discoverer-WP — owner-routing бага из post-hoc audit'а | D | — | active |
| DP.D.069 | Documentation-WP ≠ Implementation-WP — paired related-WPs, не один РП | D | — | active |
| DP.D.070 | Артефакт-режим ≠ артефакт | D | — | active |
| DP.D.071 | Декларированный bounded context ≠ Фактический bounded context | D | — | active |
| DP.D.072 | Спецификация формата ≠ Чеклист приёмки формата | D | — | active |
| DP.D.073 | Внешняя витрина ≠ Внутренняя часть платформы (по жизненному циклу + аудитории, не по технослою) | D | — | active |
| DP.D.074 | Трёхслойная модель MCP в IWE | D | Три категории MCP в IWE: платформенные (общее знание), персональные (знания пользователя), вендорские (внешние сервисы). Все платформенные — наши сервисы с RLS изоляцией. | draft |
| DP.D.075 | personal_search (семантический транспорт) ≠ Honcho (накопитель инференций) | D | personal_search — семантический доступ к источникам текста; Honcho — накопитель паттернов между запусками. В cognitive proxy pipeline: personal_search = транспорт, Honcho = память. | draft |
| DP.D.076 | Контролёр развития ≠ Оркестратор / Проводник / Навигатор / Оценщик / Аттестатор / Диагност | D | Контролёр развития (R46) — плановый фоновый сканер маркеров; не путать с шестью смежными ролями: Оркестратором (родитель), Проводником (FSM в боте), Навигатором (методология), Оценщиком (оценка ответа), Аттестатором (стадия по событиям), Диагностом (cp-профиль по запросу). Все шесть разделены по pace-слою, источнику истины и объекту внимания. | draft |
| DP.D.077 | Interface Onboarding ≠ Learning Onboarding (по объекту обучения: интерфейс vs контент) | D | — | active |
| DP.D.078 | Ценностный язык ≠ Технический язык (в user-facing копии) | D | — | active |
| DP.D.079 | Smoke Technical Vs Processing Signal | D | — | active |
| DP.D.080 | Контрольная роль ≠ Операционная роль | D | — | active |
| DP.D.089 | Cascading failure ≠ Independent failures | D | — | active |
| DP.D.090 | Structural smoke ≠ E2E smoke (по типу данных) | D | — | active |
| DP.D.091 | Выровненные на boundary шкалы ≠ Параллельные с tandem-стыком | D | — | — |
| DP.D.092 | Rate limit ≠ Value: частотный потолок и ценность — две оси, две колонки | D | — | — |
| DP.D.093 | Метка классификатора ≠ Источник ошибки | D | — | — |
| DP.D.094 | Temporal correlation ≠ Causation | D | — | active |
| DP.D.095 | IWE ≠ Платформа — правило «кто управляет экземпляром» | D | — | active |
| DP.D.096 | Парламент-модель памяти агентов — 5 элементов и инварианты | D | — | active |
| DP.D.097 | Loop control у вызывающей роли, не у вызываемой | D | — | active |
| DP.D.098 | Ground truth ≠ Self-assessment для валидации proxy-моделей | D | — | active |
| DP.D.099 | Метрика чтения ≠ метрика downstream-эффекта | D | — | — |
| DP.D.101 | Shared Module Sharing: Symlink (α) ≠ Submodule (β) ≠ Vendor Copy (γ / γ-prime) | D | — | active |
| DP.D.102 | Четыре канала событий IWE по семантике | D | — | active |
| DP.D.103 | Специализация агента через контекст ≠ специализация через дообучение | D | Два уровня специализированного агента: уровень 1 — универсальное LLM-ядро + роль в контексте (Pack + промпт); уровень 2 — дообученное LLM-ядро, роль запечена в веса. Разные оси: где живёт доменное знание. | active |
| DP.D.104 | Прогресс к награде ≠ Показ баланса | D | — | — |
| DP.D.108 | Поведенческий ≠ Технический bottleneck | D | — | active |
| DP.D.109 | TOC Bottleneck (вклад в потерю Throughput) ≠ Readiness Gap (разрыв готовности) | D | — | active |
| DP.D.110 | Pillar-текст ≠ Conversion Post | D | — | active |
| DP.D.111 | Триаж ≠ Исполнение | D | — | active |
| DP.D.112 | Cutover инфраструктуры ≠ маркетинговый запуск | D | — | active |
| DP.D.113 | AND-семантика ≠ OR-семантика для multi-storage state | D | Когда состояние сущности разнесено между volatile + durable storage'ами: AND-семантика (активна если оба источника подтверждают) требует orphan recovery loop; OR-семантика (активна если хотя бы один) безопаснее для doubt cases. | active |
| DP.D.114 | Software factory ≠ Platform — single-product vs PaaS | D | — | active |
| DP.D.115 | Distributed orchestration ≠ Monolithic orchestrator | D | — | active |
| DP.D.116 | Semantic compiler ≠ Static site generator (SSG) | D | — | active |
| DP.D.117 | Два render pipeline'а ≠ два продукта ≠ два региона | D | — | active |
| DP.D.118 | N-мерная ортогональность ролей в peer-сессии | D | — | active |
| DP.D.119 | Предметная роль ≠ структурная роль в peer-сессии | D | — | active |
| DP.D.120 | Type-string runtime drift ≠ File-replace terminology drift | D | Два класса drift'а вокабуляра. Runtime: writer и resolver обмениваются через string literal без shared enum — новые значения silently попадают в else-ветку. File-replace: переименование термина в файлах через sed — пропущенные места остаются с old name. | active |
| DP.D.121 | ТОС-горлышко системы ≠ горлышко портфеля проектов | D | — | active |
| DP.D.122 | Continuous Trend Vs Point In Time | D | — | active |
| DP.D.123 | State-Dependency Test для классификации skills | D | — | active |
| DP.D.124 | Агент-персонаж ≠ Агент-рантайм | D | — | active |
| DP.D.125 | Два независимых измерения вместо матрицы (технологический тир ⟂ содержательный progress) | D | — | active |
| DP.D.126 | Интерфейс ≠ Тир (канал доставки ортогонален технологическому уровню) | D | — | active |
| DP.D.127 | Aux Class Vs Narrative | D | — | — |
| DP.D.128 | Статический промпт ≠ интерактивный канал | D | — | active |
| DP.D.129 | Historical Membership Vs Current Channel | D | — | — |
| DP.D.130 | Технологическая ось онбординга ≠ Содержательная ось | D | — | — |
| DP.D.131 | Костюм ≠ Оснащение (тир) | D | — | active |
| DP.D.132 | Первокурсник ≠ Участник сообщества (промежуточное состояние входа ≠ полная готовность) | D | — | active |
| DP.D.133 | Три уровня изоляции данных в IWE | D | Данные в IWE изолируются на трёх независимых уровнях: БД-уровень (vault-паттерн), schema-уровень (aisystant schema), table/column-уровень (RLS + column grants). Каждый уровень защищает от разного класса нарушений. Уровни не заменяют друг друга — нарушение одного не компенсируется другим. | active |
| DP.D.136 | Предиктор выживания схемы ≠ качество дизайна | D | — | active |
| DP.D.137 | exocortex/CLAUDE.md slot (workspace-root backup) ≠ governance CLAUDE.md | D | — | active |
| DP.D.154 | Топология орг-структуры IWE: iwesys ≠ aisystant ≠ mimecosys | D | — | draft |
| DP.D.155 | Active Day Definition | D | — | active |
| DP.D.177 | Прокси-метрика ≠ прямая метрика | D | — | draft |
| DP.D.179 | Политика при сбое ≠ видимость сбоя в мониторинге | D | — | draft |
| DP.D.182 | IWE как рабочая среда ≠ образовательная платформа | D | — | active |
| DP.D.183 | Машинный ноль в измеренном поле ≠ результат измерения «ноль» | D | — | active |
| DP.D.184 | Пустая витрина ≠ отсутствие данных в источнике | D | — | active |
| DP.D.186 | Документация снижает вероятность ошибки ≠ инвариант в коде устраняет её физически | D | — | active |
| DP.D.187 | SYNC-CORE (общее ядро инструкций) ≠ Claude-specific inject-hook | D | — | active |
| DP.D.190 | `updated_at` строки-контейнера ≠ актуальность данных внутри строки | D | — | active |
| DP.D.191 | Mitigation ≠ Fix: статус дефекта остаётся открытым при снижении риска без устранения причины | D | — | active |
| DP.D.192 | Per-event rule engine ≠ stateful accumulation: разные вычислительные модели, разные компоненты | D | — | active |
| DP.D.193 | Имя поля/константы ≠ семантика текущей операции | D | — | active |
| DP.D.194 | Sanity check ≠ валидация на реальном масштабе | D | — | active |
| DP.D.195 | U.Method холоничен — U.Role нехолонична | D | — | — |
| DP.D.196 | Org Role Assignment Vs Infra Readiness | D | — | draft |
| DP.D.204 | Method Map Vs State Axis | D | Каталог методов изменения состояния объекта — слой рычагов внутри существующей оси, не новое измерение модели состояний. Тест: это состояние объекта или инструмент воздействия на состояние? | draft |
| DP.ECON.001 | Points Engine — движок начисления баллов | ECON | Доменная модель системы баллов: сущности, инварианты, формула, потоки. Source-of-truth для Points Engine (WP-121, WP-311). Текущая реализация: база rewards (Neon). | draft |
| DP.EXOCORTEX.001 | Модульный экзокортекс | EXOCORTEX | 3-слойная архитектура инструкций ИИ-агентов: CLAUDE.md + Memory + repo-CLAUDE.md | draft |
| DP.FM.001 | Информация как знание | FM | Необработанная информация ошибочно принимается за формализованное знание без экстракции | draft |
| DP.FM.002 | Смешение слоёв | FM | Смешение слоёв архитектуры платформы: код в Pack, знания в Downstream, UI в архитектуре | draft |
| DP.FM.003 | Контекстная слепота AI-агента | FM | Ускорение генерации модели без ресурсов на добычу контекста = ускорение самообмана. AI-агент не может сам получить живой контекст из реальной жизни | draft |
| DP.FM.004 | Narrow Pregeneration Scope | FM | — | draft |
| DP.FM.005 | Дрейф модель–реальность (Model-Reality Drift) | FM | AI-агент без петли измерения деградирует в самосогласованный текст: внутренняя непротиворечивость растёт, но близость к реальности падает. Двойной дрейф: мир меняется + цели агента дрейфуют | draft |
| DP.FM.006 | Когнитивный долг как следствие агентного ИИ | FM | Агентный ИИ производит код быстрее, чем разработчики успевают строить теорию системы. Техдолг — в коде, когнитивный долг — в головах. Программа — это теория в головах, код — лишь проекция | draft |
| DP.FM.007 | Дрейф представлений (View Drift) | FM | View-файлы (README, CLAUDE.md, REGISTRY, посты) рассинхронизируются с model-файлами (Pack-сущности, код, файловая система). Причина: отсутствие автоматической валидации claims в view-файлах при изменении model. | draft |
| DP.FM.008 | Auto-Promote Without Confirmation | FM | Автоматический промоут черновика без подтверждения пользователя — нарушение human-in-the-loop gate | active |
| DP.FM.009 | Protocol Hardcoded Script Path | FM | Протокол ОРЗ хардкодит абсолютный путь к скрипту — ломается при любом перемещении скрипта. Симптом: exit 127 (no such file or directory). | resolved |
| DP.FM.010 | Agent Failure Patterns Catalog | FM | Каталог повторяющихся паттернов провалов Claude-агента в рамках IWE. Корень дерева защит: паттерн → правило → детектор → Capture. Плоская нумерация P1-PN, постепенное развёртывание в отдельные DP.FM.XXX по мере обкатки | draft |
| DP.FM.011 | Not Capturing Patterns | FM | Агент реагирует на провал записью нового правила в feedback_*.md, не обобщая его в паттерн. Правила множатся без роста compliance. Мета-корень дерева провалов: без защиты от P1 деградируют все остальные детекторы | draft |
| DP.FM.012 | Lexical Deduplication (Lossful Ontology Merge) | FM | ИИ-агент при запросе 'убери дубли' выполняет лексическую нормализацию (совпадение слов/имён) вместо онтологической унификации (совпадение сущностей). Результат: семантически разные концепты сливаются в один, различения теряются без следа. | active |
| DP.FM.013 | Conservative Rewrite Failure (Смысловая контаминация при перефразировании) | FM | При пересказе, переводе формата или summary ИИ-агент добавляет новые предположения и термины, которых не было в исходнике. Граница между 'перевыражением той же вещи' и 'переинтерпретацией с сочинительством' не контролируется. | active |
| DP.FM.014 | Legacy Port Jump (Прыжок в новый дизайн без проверки legacy) | FM | При замене legacy-компонента (миграция из внешней системы, старой кодовой базы, LMS) агент прыгает сразу в проектирование нового дизайна, не выяснив как работает существующий механизм. Результат — перерасход часов в 3-5 раз или потеря рабочего решения. | active |
| DP.FM.015 | False-Positive Capture Detection (grep vs awk) | FM | — | active |
| DP.FM.016 | Decay конфигурационных путей | FM | — | active |
| DP.FM.017 | Asymmetric Env Cleanup (Асимметричная очистка env-переменных) | FM | Smoke-test устанавливает несколько env-переменных с эфемерными путями (/tmp/iwe-smoke-*), но cleanup сбрасывает не все → ночные/non-interactive запуски падают с path-ошибками. | active |
| DP.FM.018 | Markdown Display-маркеры в data-полях (Markdown Markers in Data Fields) | FM | Поля Markdown-таблиц содержат display-разметку (**bold**, ~~strike~~), корректную для рендеринга, но ломающую downstream text-processing (sed, awk, jq, commit messages). | active |
| DP.FM.019 | L3 Identity Leak (Утечка авторской идентичности в шаблон) | FM | §9 (авторское) FMT-шаблона содержит конкретные имена/ID/пути пилота вместо {{PLACEHOLDER}} — при обновлении шаблона через update.sh эти данные распространяются на всех пользователей. | active |
| DP.FM.020 | Gateway SC без security disclosure для upstream credentials | FM | SC для Gateway-компонента с upstream-proxy не содержит явного раздела «Безопасность» с MITM-disclosure. Потребитель не знает, что Gateway видит его OAuth-токены при proxying. Нарушение принципа informed consent в security архитектуре. | active |
| DP.FM.021 | Zero-slot blocks min aggregation | FM | — | — |
| DP.FM.022 | systemd-minimal-path | FM | — | active |
| DP.FM.023 | service-user-credentials-path | FM | — | active |
| DP.FM.024 | git-pull-in-production — слияние build/release/run в агентах и launchd | FM | — | active |
| DP.FM.025 | Монорепо с независимыми сервисами — нарушение 12-factor F1 | FM | — | active |
| DP.FM.026 | .env в git history — утечка secrets + обязательные шаги ликвидации | FM | — | active |
| DP.FM.027 | Railway Missing Auto-Deploy (Ручной деплой без git-интеграции) | FM | Railway-проект развёртывается вручную (кнопкой), а не через git-webhook. Признак: отсутствие RAILWAY_GIT_* env-переменных и reason='deploy'/'redeploy' вместо 'github_push' в deployments API. Следствие: код в git не соответствует задеплоенному без явного ручного действия. | active |
| DP.FM.028 | Event Coverage Gap — новый модуль без аудита эмиссии событий | FM | При добавлении нового workflow-модуля не проводится аудит event coverage: модуль доставляет пользовательские действия без эмиссии domain_event. Downstream системы (stage_evaluator, activity hub) видят пустой stream — активность пользователя не учитывается. | active |
| DP.FM.029 | Cross-Platform Path Leak (Утечка платформо-специфичных путей) | FM | В конфигурации или коде кросс-платформенного инструмента прописан платформо-специфичный путь (macOS /Users/... slug, Windows C:\...). На целевой платформе (Linux/сервер) путь не существует, инструмент молча выдаёт WARN и продолжает работу — без явной ошибки. | active |
| DP.FM.030 | Compliance Matrix Narrative Drift (дрейф нарратива от ячеек матрицы) | FM | При инкрементальном заполнении compliance-матрицы нарратив-секция обновляется реже ячеек таблицы. Числа в тексте расходятся с реальными counts — drift обнаруживается только при независимом review. | active |
| DP.FM.031 | Hardcoded Os Path | FM | — | active |
| DP.FM.032 | Repair-Pass Stale-Hash Blind Spot (Слепое пятно устаревшего файла при repair-pass) | FM | Repair-pass проверяет только отсутствие файла (! -f), но не его актуальность (hash vs source). Если файл существует, но содержимое расходится с FMT-source, он остаётся без обновления. Silent stale-регрессия. | draft |
| DP.FM.033 | Bash arithmetic increment под set -e (Bash Arithmetic Increment Under set -e) | FM | Конструкция `((var++))` возвращает exit code 1 при var=0 (post-increment) — под `set -e` вызывает тихий abort скрипта без сообщения об ошибке. | active |
| DP.FM.034 | Pack-шифры в теле текста руководства | FM | — | active |
| DP.FM.035 | CI live-config patch — iteration debt от хрупких примитивов | FM | — | active |
| DP.FM.036 | WakaTime Measurement Scope Bias (Систематическое завышение через трекер без доменного scope) | FM | Системный трекер активности (WakaTime, IDE session, GitHub commits) измеряет все репо без фильтра по домену. При использовании как прокси «инвестиций в X» — систематическое завышение в 3-5×. | draft |
| DP.FM.037 | Парсинг состояния по заголовку шаблона vs значению из frontmatter (Markdown Header Presence vs Frontmatter Value State Detection) | FM | Детектор состояния использует `grep` по заголовку секции (`### 🔴 Critical`), который присутствует в шаблоне всегда — false-positive при пустой секции. Состояние должно парситься из значения в YAML frontmatter, а не из наличия заголовка. | active |
| DP.FM.038 | Silent-Pass Validator on Missing Input (Валидатор зеленеет на отсутствующем входе) | FM | Валидатор на отсутствующем или пустом входе возвращает exit 0 (нечего нарушать), создавая false-green в CI/pre-commit. Опечатка в пути или несостоявшийся checkout → нулевая проверка → ложно-положительный сигнал. | draft |
| DP.FM.039 | Zero-Data Phase Cold Start (Нулевые значения при запуске нового metric pipeline) | FM | Новый metric pipeline после запуска видит ноль у всех пользователей — исторические данные ещё не накоплены в новом формате. Без human-fallback система выдаёт «нет активности» → неверные показатели с первого дня. | draft |
| DP.FM.040 | Silent-Null Parser on Unknown Syntax (Парсер молча возвращает null) | FM | Парсер ad-hoc форматов возвращает '' / null на не распознанный синтаксис вместо exception. На пустых данных тесты зелёные; слепая зона активируется когда поле начинают заполнять — все записи проходят валидацию пустыми. | draft |
| DP.FM.041 | Dedup Slice False Positive | FM | — | active |
| DP.FM.042 | Same Schema Neon Dbs | FM | — | draft |
| DP.FM.043 | Case Enum Assumption | FM | — | draft |
| DP.FM.044 | Retroactive Backfill Regime Mismatch | FM | — | draft |
| DP.FM.045 | log-after-success violation: idempotency-log записан ДО side-effect → retry невозможен | FM | — | — |
| DP.FM.046 | Render-queue timeout — отсутствующий deadline на вызов подзадачи | FM | Задание зависает в очереди навсегда, потому что воркер ждёт ответа от подзадачи без явного timeout. Диагностика: open-sessions log. Признак: задание в статусе «выполняется» дольше expected_max. | active |
| DP.FM.047 | Third Party Pii Vendor Gate | FM | — | draft |
| DP.FM.048 | Cf Bot Fight Mode Xhr Block | FM | — | active |
| DP.FM.049 | Document-centric analysis yields false bottleneck | FM | — | active |
| DP.FM.050 | Markdown Bold Regex Punctuation | FM | — | active |
| DP.FM.051 | On Conflict Nullable Unique Incompleteness | FM | — | draft |
| DP.FM.054 | Linter-зелёный ≠ структура body-текста | FM | — | — |
| DP.FM.055 | deprecated_files в manifest ≠ удалён из runtime-runner | FM | — | — |
| DP.FM.056 | Deprecated Not Deleted Runner Out Of Sync | FM | — | draft |
| DP.FM.057 | cp.iwe в контенте Guides 1-2 — нарушение bounded-context | FM | Включение cp.iwe (Machine-level competence, ступень 3+) в контент Guides 1-2 (ступени 1-2) создаёт скрытую зависимость: пользователь не может освоить базовый материал без навыков, которых у него ещё нет. | active |
| DP.FM.058 | Pilot-инсталляция с открытым дефолтом = silent PII-accumulation | FM | — | active |
| DP.FM.059 | Hook Command Relative Path | FM | — | draft |
| DP.FM.060 | Half Migration Manifest Runner Split | FM | — | draft |
| DP.FM.061 | Ci Optional Secret Hard Fail | FM | — | draft |
| DP.FM.070 | Dispatcher Git Reset Race Condition | FM | — | active |
| DP.FM.072 | Не-канонические формы понятий в introduces и pack_refs | FM | — | — |
| DP.FM.073 | Protocol Coverage Gap Mentioned Not Enforced | FM | — | draft |
| DP.FM.074 | State-machine callback handler without router wire-up = silent dead-end | FM | — | — |
| DP.FM.075 | deprecated-files-as-todo-tracker | FM | Запись артефакта в `deprecated_files` до удаления всех зависимостей в коде — превращает список устаревших в TODO-трекер, что вызывает runtime-drift при следующем update. | draft |
| DP.FM.077 | Overstated Validator Coverage in Documentation (Документация заявляет автоматическое покрытие, которое не выдержано) | FM | Документация валидатора/линтера/детектора заявляет 'автоматически ловит этот класс ошибок' без указания scope. Реально детектор покрывает только subset (например, regex по конкретным путям). Пользователь полагается на автоматику для всего класса → дрейф проходит мимо. | draft |
| DP.FM.078 | Ghost canonical pointer | FM | — | active |
| DP.FM.079 | impact_group как множитель — математический взрыв в формуле вознаграждения | FM | — | active |
| DP.FM.080 | Закрытие РП после первого фикса при многодефектном симптоме | FM | — | — |
| DP.FM.081 | Double-count в probe-пути: одно событие → два инкремента деградации | FM | — | — |
| DP.FM.082 | «4 кирпича = Президент» — Парламент-антипаттерн с единым посредником | FM | — | active |
| DP.FM.083 | Empty Field Url Injection | FM | — | active |
| DP.FM.084 | OAuth+CDN миграция без redirect_uri pre-flight: полный outage вместо частичного | FM | — | — |
| DP.FM.085 | Hook-installer anti-patterns: --no-verify, double-run, no-backup, no-diff-check | FM | — | — |
| DP.FM.086 | Dangling Intent: РП pending без dueDate | FM | — | active |
| DP.FM.087 | Watchdog false-positive: молодой скрипт как overdue | FM | — | active |
| DP.FM.088 | Done-фаза с открытыми чек-боксами — скрытый технический долг | FM | — | active |
| DP.FM.089 | Test Blast Radius Shared Flow Io | FM | — | proposed |
| DP.FM.090 | Числовой порядковый guard в multi-producer turn-log вместо семантического | FM | — | — |
| DP.FM.091 | God-Table Anti-Pattern (склейка несвязанных доменов в core-таблице) | FM | — | active |
| DP.FM.092 | Fire-and-forget temporal coupling со streak/бизнес-логикой | FM | — | active |
| DP.FM.093 | Retry storm guard создаёт orphaned content при деградации API в момент первой попытки | FM | — | active |
| DP.FM.094 | Бинарный счётчик advance маскирует легитимные причины non-advance (DLQ-blocked) | FM | — | active |
| DP.FM.095 | Feature-flag activated without ALTER FUNCTION | FM | — | — |
| DP.FM.096 | Config without emitter — invisible zero events | FM | — | — |
| DP.FM.097 | Deployment Path Drift — Home vs Repo | FM | — | — |
| DP.FM.098 | SM-Mutex Guard Coverage Gap — Queue-Based Flows Bypass Guard | FM | — | — |
| DP.FM.099 | NOTIFY-подписка живёт на коннекте — смерть conn = весь event-loop зомби | FM | — | active |
| DP.FM.100 | Snapshot stale → неверный диагноз без сигнала | FM | — | — |
| DP.FM.101 | Rule-engine NOOP при отсутствии записи — silent event drop | FM | — | active |
| DP.FM.102 | Boolean flag с hardcoded константой в ветке вычисления — silent underpayment | FM | — | active |
| DP.FM.103 | Coverage-скрипт без фильтра scope агрегирует FAIL из соседних guide | FM | — | draft |
| DP.FM.104 | Отсутствие обратной функции identity-lookup | FM | — | active |
| DP.FM.105 | Внутренний health-probe слеп к собственным падениям | FM | — | active |
| DP.FM.106 | Anthropic API usage limit — терминальный blocker automation-pipeline | FM | — | active |
| DP.FM.107 | Volatile Function Upsert Trigger Cascade | FM | — | — |
| DP.FM.108 | Owner-резолвер с пустым default из единственного источника (adopted-sovereign trap) | FM | — | active |
| DP.FM.109 | Sentinel empty-string → прошлый слот планировщика | FM | — | — |
| DP.FM.110 | Unix socket без protocol handshake → пустой ответ | FM | — | — |
| DP.FM.111 | Спящее правило в session-memory: trust < cut-off → не попадает в reminders | FM | — | active |
| DP.FM.113 | Regex `search()` глотает второе нарушение в multi-violation validators | FM | — | — |
| DP.FM.114 | Adapter Dependency Silent Regression | FM | — | accepted |
| DP.FM.115 | Peer Agent Overwrite Without Read | FM | — | — |
| DP.FM.116 | External Id Path Traversal | FM | — | draft |
| DP.FM.117 | Двойной учёт компонента в compound-формуле | FM | — | — |
| DP.FM.118 | Двойное значение метрики в названии (theoretical vs operational) | FM | — | — |
| DP.FM.119 | Concurrent Writers Break Threshold Logic | FM | — | active |
| DP.FM.120 | Маскировка нулей вместо root-fix в диагностике метрик | FM | — | active |
| DP.FM.121 | Dry-run side-effect — нарушение read-only обещания | FM | — | active |
| DP.FM.122 | Spec без impl — спецификация ушла вперёд кода | FM | — | active |
| DP.FM.123 | Reverse proxy режет long-running HTTP-handler — config application-timeout врёт | FM | — | — |
| DP.FM.124 | lru_cache для async resource с lifecycle: leak + cross-loop errors | FM | — | — |
| DP.FM.125 | Short-name fallback в authorization scope-check: cross-tenant bypass | FM | — | — |
| DP.FM.126 | Полиморфный return type на shared helper ломает downstream callsites молча | FM | — | — |
| DP.FM.127 | Python 3.9: тип-аннотации → TypeError без from __future__ import annotations | FM | — | — |
| DP.FM.128 | Pytest: тест не запускается из-за ImportError при collection (Python ≤3.9) | FM | — | — |
| DP.FM.129 | Broken Symlink Silent Config Empty | FM | — | — |
| DP.FM.130 | Os Expanduser No Shell Vars | FM | — | active |
| DP.FM.132 | Microservice Tier Sot Mismatch | FM | — | — |
| DP.FM.133 | Backup Restore No 3Way Merge | FM | — | active |
| DP.FM.134 | Vocabulary Split Aux Subsections | FM | — | — |
| DP.FM.135 | Projection Rule No Backfill Fallback Mask | FM | — | — |
| DP.FM.137 | Asymmetric Alert Suppression Paths | FM | — | — |
| DP.FM.138 | Shared Db Without Env Discriminator | FM | — | — |
| DP.FM.139 | Llm Proxy Default Timeout Too Short | FM | — | — |
| DP.FM.140 | Cutover отключает основной путь, оставляя side-channel активным | FM | — | — |
| DP.FM.141 | Shared queue без tenant-ключа: dedup-scope распространяется между инстансами | FM | — | — |
| DP.FM.142 | New codepath no retry-symmetry — новый code-path без retry-симметрии с legacy-path | FM | — | active |
| DP.FM.143 | Ppid Fallback Stale Pidfile Multiagent | FM | — | — |
| DP.FM.144 | Side Effect Check Blocks Primary Flow | FM | — | — |
| DP.FM.145 | FDW-только-READ: cross-DB write в SQL-миграции молча провалится | FM | — | — |
| DP.FM.146 | Unconditional helper return = always-fires gate: гейт срабатывает для всех пользователей | FM | — | — |
| DP.FM.147 | aiogram Bot() без try/finally session.close() → leak HTTP-коннектов в scheduler | FM | Bot() создаётся per-call в scheduler, session.close() стоит после падающих операций без try/finally — при исключении HTTP-соединение к Telegram остаётся открытым, дескрипторы растут. | — |
| DP.FM.148 | Regex Detector Semantic Blindspot | FM | Regex-детектор стиля видит только морфологию, не смысл — ловит одно орфографическое правило (98% срабатываний), семантические нарушения не замечает, создавая видимость покрытия. | — |
| DP.FM.149 | Channel Style Bleed Peer Synthesis | FM | Синтезатор читает технические turn-файлы и продолжает их стиль при записи отчёта для пилота — английские термины и машинные маркеры переползают из доказательного слоя в pilot-facing. | — |
| DP.FM.150 | Silent Rule Decay No Cost | FM | Детектор пишет лог, агрегатор поднимает напоминание по порогу N в неделю — при редких нарушениях критического правила порог молчит, нарушитель не видит ошибку, правило перестаёт действовать. | — |
| DP.FM.151 | Subscription gate multi-path divergence | FM | В OAuth с двумя типами токенов (JWT и opaque) проверка подписки дублируется в нескольких путях кода — фикс одного пути не покрывает другой, один тип клиента проходит, другой блокируется при том же тарифе. | — |
| DP.FM.152 | tracked-dir-added-to-gitignore: Добавление отслеживаемой git-папки в .gitignore без untracking | FM | — | — |
| DP.FM.153 | Перемежающийся 401 со статическим ключом = прокси или env, не ключ | FM | — | — |
| DP.FM.154 | Commit-without-push на сервере → отложенная дивергенция | FM | — | — |
| DP.FM.155 | Cross Db Trigger Boundary | FM | — | active |
| DP.FM.156 | Agent Tool Check Before Pilot Escalation | FM | Агент просит пилота выполнить авторизацию, пройти UI-шаги или дать доступ, не проверив сначала доступные инструменты — нарушение принципа 'инструменты до эскалации'. | — |
| DP.FM.157 | Cloud Backup Wrong Claude Layer | FM | В многоуровневой топологии IWE облачный бэкап имеет доступ только к одному уровню (governance), но пишет в слот другого уровня (workspace-root) — каждый день правильный локальный бэкап перезаписывается неправильным. | — |
| DP.FM.158 | Xargs Word Splitting Spaces False Fail | FM | Использование pipe-xargs для проверки существования файлов с пробелами в имени: «DayPlan 2026-06-13.md» разбивается на «DayPlan» и «2026-06-13.md» — оба несуществующих → false FAIL на каждом Day Close. | — |
| DP.FM.159 | Creation-flow gap between linked identity providers | FM | Если две identity-системы связаны односторонним ETL (читает из Б, не пишет в Б), регистрация в системе А не создаёт идентичность в Б. Пользователь с аккаунтом А упирается в форму входа Б без аккаунта — UX-симптом «не принимает пароль». | — |
| DP.FM.160 | Интерфейс без ядра порождает галлюцинацию данных | FM | Когда интерфейсный агент (бот, chatbot, voice UI) не подключён к источнику данных через tool-вызов, LLM не возвращает ошибку — генерирует правдоподобный ответ из параметров модели. Симптом неотличим от правильного ответа без сверки с SoT. | — |
| DP.FM.161 | pack-event-name-drift: Pack документирует выдуманное имя события, в коде другое имя | FM | — | — |
| DP.FM.162 | РП-контекст: vapor-claim о готовом компоненте (дрейф карточки от кода) | FM | — | draft |
| DP.FM.163 | Локально зелено, в CI красно: node_modules маскирует stale package-lock | FM | — | draft |
| DP.FM.164 | jose JWKS DNS-failure маскируется под request timed out | FM | — | active |
| DP.FM.165 | Foreground Shell Orphan Ide Extension | FM | — | draft |
| DP.FM.166 | Schema Consumer Contract Breach | FM | — | draft |
| DP.FM.167 | Тихий False от upstream отключает except-fallback | FM | — | draft |
| DP.FM.168 | Метрика=0 для активного пользователя: code-review фильтра до проверки raw-данных | FM | — | draft |
| DP.FM.169 | Тихий fallback в content pipeline: acceptance PASS при деградации содержания | FM | — | draft |
| DP.FM.170 | Literal Guard Pattern Mismatch On Day One | FM | Guard проверяет наличие коммита через жёсткое совпадение текста, но реальные коммиты используют другой синтаксис. Guard сломан в момент деплоя. | — |
| DP.FM.171 | Ui Visibility Vs Code Access | FM | — | — |
| DP.FM.186 | Append Only Phantom Early Writer | FM | — | — |
| DP.FM.187 | Raw Template Execution Silent Artifacts | FM | — | — |
| DP.FM.188 | Shared Db Owner Nonattribution | FM | — | — |
| DP.FM.189 | Hash Without Prev Chain False Immutability | FM | — | — |
| DP.FM.190 | Validator No Enforcement Point | FM | — | — |
| DP.FM.191 | Source-of-truth в смертной папке РП: закрытие РП ломает внешние ссылки | FM | SoT-файл создан внутри рабочей папки РП; при закрытии РП уходит в архив — внешние ссылки из платформенных файлов становятся битыми без сигнала. | active |
| DP.FM.192 | Subshell Redirect Silences Exit Code | FM | — | — |
| DP.FM.193 | Git Dead Hook Core Hookspath | FM | — | — |
| DP.FM.194 | Launchd Stale Pid Port Occupation | FM | — | — |
| DP.FM.195 | Retroactive history cleanup to hit deadline (ретроактивная чистка истории ради дедлайна) | FM | — | active |
| DP.FM.196 | Deferred Sql In Auto Executed Migration File | FM | — | — |
| DP.FM.197 | Replay Tool Misidentified As Incoming Buffer | FM | — | — |
| DP.FM.198 | Crypto Shredding Not Gdpr Erasure | FM | — | — |
| DP.FM.199 | Role Revoke Schema Owner Bypass | FM | — | — |
| DP.FM.200 | Audit Log Missing Source Service | FM | — | — |
| DP.FM.201 | Bsd Gnu Sed Ampersand Escaping | FM | — | — |
| DP.FM.202 | Multiple Registries One Entity Drift | FM | — | — |
| DP.FM.203 | Deployed Consensus Not Final Verification | FM | — | — |
| DP.FM.204 | Multi Row Insert Forks Trigger Chain | FM | — | — |
| DP.FM.205 | Fsm Intermediate State Without Exit Path | FM | — | — |
| DP.FM.206 | Ddl In Ensure Schema Locks Every Run | FM | — | — |
| DP.FM.207 | Grep Keyword Not Anchored To Header False Green | FM | — | — |
| DP.FM.208 | Bash32 Ifs Tab Nosplit | FM | — | draft |
| DP.FM.209 | Sql Injection Fstring Parameter | FM | — | draft |
| DP.FM.210 | Zsh Bsd Grep Multiline False Green | FM | — | draft |
| DP.FM.211 | Gitignore Env Pattern Incomplete | FM | — | draft |
| DP.FM.212 | git filter-branch --all уничтожает refs/stash (multi-parent) | FM | — | — |
| DP.FM.213 | git filter-branch на текущей ветке синхронизирует worktree — файлы физически удаляются | FM | — | — |
| DP.FM.214 | Zsh Word Split Bsd Grep Multiline False Green | FM | — | draft |
| DP.FM.215 | Semaphore Agent Id Race Parallel Sessions | FM | — | draft |
| DP.FM.216 | Multi Owner Aggregate Policy Granularity | FM | — | draft |
| DP.FM.217 | Shell Pid Not Agent Session Pid | FM | — | draft |
| DP.FM.218 | Wrong Diagnosis Hides Real Bug | FM | — | draft |
| DP.FM.219 | Type Cast In Where Breaks Index | FM | — | — |
| DP.FM.220 | Health Check Ddl Side Effect False Fail | FM | — | — |
| DP.FM.224 | Smart Sync Stub Exists Not Local | FM | — | — |
| DP.IWE.001 | Intellectual Work Environment (IWE) | IWE | IWE — персональная интегрированная среда для интеллектуальной работы. Описывается через 5 архитектурных видов (ISO 42010): системы (U.System), описания (U.Description), роли (U.RoleAssignment), методы (U.MethodDescription), рабочие продукты (U.Work). Триада A.7: Роль → Метод → Рабочий продукт. Позиционирование: почему именно IWE, а не агенты/экзокортекс/FPF по отдельности. | draft |
| DP.IWE.002 | IWE Template & Setup | IWE | Практическое знание о шаблоне IWE: установка, ежедневная работа (ОРЗ), кастомизация (strategy_day, AUTHOR-ONLY зоны, конфиги), роли, обновление, FAQ. Source-of-truth для бота и MCP. | draft |
| DP.IWE.003 | Gateway-архитектура IWE | IWE | — | active |
| DP.IWE.004 | Интерфейсы IWE — различения клиентов | IWE | — | active |
| DP.IWE.005 | Local MCP Gateway (in-process multi-agent layer) | IWE | — | draft |
| DP.IWE.006 | Personal Guide Channels | IWE | — | draft |
| DP.IWE.007 | 5 природ IWE (Five Natures of IWE) | IWE | Пять UX-природ IWE — чем IWE является для пилота: Мастерская (среда ежедневной работы), Железный человек (костюм-расширитель), Аватар (узел сети сопроизводителей), Тамагочи (выращиваемый питомец, требующий ухода), Наставник (ведёт по траектории развития). Дополняет 5 архитектурных видов DP.IWE.001 (ISO 42010) — другая онтологическая ось: природы про «чем IWE является для пилота», виды архитектуры про «как описывать IWE». Порядок природ отражает приоритет: работа → жизнь → обучение. Источники: пост club-126 (4 мая 2026), посты TG 675 + 679 + 143, уточнение пилота 2026-05-18 (+5-я природа), уточнение пилота 2026-05-20 (Со-творец → Тамагочи, reorder). | draft |
| DP.IWE.008 | BYOB (Bring Your Own Base) | IWE | — | draft |
| DP.IWE.009 | IWE Perimeter (Контур IWE) | IWE | — | draft |
| DP.IWE.010 | IWE Machine (Машина IWE) | IWE | — | draft |
| DP.IWE.011 | IWE Runtime Host Contract | IWE | — | draft |
| DP.IWE.011-adapter-claude-code | Claude Code Adapter for IWE Host Contract | IWE | — | active |
| DP.IWE.011-adapter-headless | Headless Adapter for IWE Host Contract | IWE | — | active |
| DP.KR.001 | Маршрутизация знаний IWE | KR | Полная карта маршрутизации: какой тип контента куда записывать — от ZP до memory/, от Pack до 0.9.Inbox. Единый source-of-truth для агента и пользователя | draft |
| DP.KR.030 | Принцип триады учёт-доступ-аудит | KR | Три функции институционального контроля — Учёт, Доступ, Аудит — должны быть структурно разделены. Совмещение любых двух из трёх в одной роли нарушает принцип независимости контроля. KR.030 = foundation серии (delivered WP-214). KR.031–033 = refinement-принципы каждой ветки; отложены, создаются при отдельном РП по необходимости. Серия KR.030–039 зарезервирована. | active |
| DP.M.001 | Извлечение знаний | M | Трансформация сырой информации в Pack-совместимые сущности и DS docs/ через обнаружение, классификацию, двойной routing и формализацию | draft |
| DP.M.002 | Применение стратегического DDD | M | Метод применения стратегического DDD к Pack и экзокортексу: BC mapping, UL extraction, Context Map для inter-agent integration | draft |
| DP.M.003 | Context Engineering Protocol | M | Метод проектирования контекста ИИ-агента: Write/Select/Compress/Isolate → CLAUDE.md + memory/ + Pack layers | draft |
| DP.M.004 | Адаптивная персонализация по состоянию | M | Адаптация контента развития (промпты, bloom_level, тематика) на основе состояния пользователя из теста систематичности | draft |
| DP.M.005 | АрхГейт (ArchGate) | M | Блокирующая оценка архитектурного решения по 7 характеристикам (ЭМОГССБ): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность, безопасность. Без прохождения — решение не принимается | active |
| DP.M.006 | Самопроверка вайб-режима (Vibe-Check) | M | Метод оценки допустимости вайб-режима работы по 6 характеристикам проектной ситуации. Определяет: вайб допустим или нужна профессиональная работа | draft |
| DP.M.007 | Intervention Loop (Петля интервенции) | M | Метод замыкания цикла действие-измерение-обновление для AI-агентов на LLM-платформе: зондирование реальности, фиксация невязки, обновление модели. Компенсирует отсутствие world model | draft |
| DP.M.008 | Культура работы IWE (Work Culture) | M | Культура работы IWE: 14 элементов в трёх разрезах — протоколы (формализованные последовательности), навыки (нарабатываемые по ситуации), форматы (стандарты оформления). #12 ТО = поддержание текущего состояния. #14 Эволюция = развитие системы. Реализация — в DS/FMT, инварианты — здесь | active |
| DP.M.009 | Расширяемость шаблонных систем (Template Extensibility) | M | Метод проектирования расширяемости в системах с платформенным шаблоном и пользовательскими инстансами. Три паттерна (drop-in, overlay, 3-way merge), критерии выбора, протокол обновления с обнаружением противоречий | draft |
| DP.M.010 | Управление жизненным циклом рабочего продукта | M | Метод гарантирует консистентность РП-объекта во всех хранилищах IWE на протяжении всего цикла: создание → активная сессия → закрытие → архивация. Единственная роль координации — Регистратор РП (DP.ROLE.037). | active |
| DP.M.011 | Агрегация captures из множества источников | M | Единый inbox-файл (captures.md) наполняется автоматически из 4 каналов с маркерами источника для идемпотентной обработки Экстрактором | draft |
| DP.M.012 | Machine-Check Postcondition | M | — | active |
| DP.M.013 | Security Audit Cadence | M | Метод управления аудитом безопасности платформы через три уровня периодичности: event-driven (каждое архитектурное решение, ~0 ₽), weekly light-check (2 мин, ~0 ₽), daily automated deep-scan (systemd-timer + subagent с context isolation, ~$1.5/день). Архетип применим к любой platform с security-требованиями. | active |
| DP.M.014 | Evaluator Worker | M | — | draft |
| DP.M.015 | Четырёхслойная каскадная зависимость в activity-based геймификации | M | — | draft |
| DP.M.016 | Диагностика зрелости домена (3 вопроса) | M | — | active |
| DP.M.017 | Runtime Tool Discovery через JSON-RPC | M | LLM-клиент строит список tool в runtime через tools/list JSON-RPC с TTL-кэшем (15 мин) и fallback на last-known-good при недоступности сервера. Hardcoded список tool = антипаттерн. | draft |
| DP.M.018 | External Data Fallback Hierarchy | M | — | active |
| DP.M.019 | Промоция скрипта из авторского IWE в платформенный шаблон (L3→L1) | M | 7-шаговый процесс перевода скрипта из авторского IWE (L3) в платформенный шаблон FMT (L1): проверка коллизий, параметризация, smoke-test в 3 кейсах, обновление манифеста, коммит feat: promote. | draft |
| DP.M.020 | Паттерн необязательной зависимости скрипта через params.yaml | M | Паттерн проектирования shell-скриптов с опциональными внешними зависимостями: ключ в params.yaml с дефолтом '' (пустая строка), graceful skip при пустом значении, warning+exit 1 при несуществующем пути. Три обязательных smoke-кейса. | draft |
| DP.M.021 | GitHub App Platform Integration | M | — | — |
| DP.M.022 | Cache-safe Personal Dashboard (снапшот + daily sync) | M | — | — |
| DP.M.023 | Chaining nightly tasks через фиксированный offset | M | Зависимые ночные задачи (producer → consumer) запускаются с фиксированным N-минутным offset вместо явной зависимости After=/ExecStartPost. Устойчив к задержкам producer'а. | active |
| DP.M.024 | Fallback-поле для NULL в темпоральных расчётах с legacy-данными | M | — | draft |
| DP.M.025 | Волновое развёртывание (Wave Rollout) | M | — | draft |
| DP.M.026 | git-fork-push-pattern | M | — | active |
| DP.M.027 | 12-factor Matrix для инвентаризации production deployment | M | Метод систематической инвентаризации всех production deployment units через матрицу F1-F12. Позволяет обнаруживать системные дефекты (например, floating deps у всех Python-сервисов) за один проход по стеку. | active |
| DP.M.028 | Stateless Worker — PostgresStorage + CursorCache + batched-flush | M | — | active |
| DP.M.029 | Cross-verification CRITICAL-флагов автоматического аудита | M | — | active |
| DP.M.030 | F9 Disposability — двухкомпонентный паттерн worker | M | Для 12-factor F9 (Disposability) в event-driven workers нужны два независимых механизма: (1) SIGTERM handler для graceful shutdown, (2) cursor-based idempotency для crash safety. Только их комбинация даёт полный F9. | active |
| DP.M.031 | Reusable Flow Export — экспортируемая функция для множественных точек входа | M | Функция UI-flow (consent, onboarding, активация) оформляется как reusable export из своего модуля, а не как inline-код в одном handler. Позволяет нескольким entry points (deep-link, команда, кнопка, QR-код, UTM-параметр) делегировать единой реализации без дублирования. | active |
| DP.M.032 | Предпочтение MD-формата для плотного LLM-контекста | M | MD-формат на 26% короче HTML при одинаковой точности распознавания Haiku. Рекомендация: использовать MD для плотного структурированного контекста агента; таблицы — исследовать отдельно. | active |
| DP.M.033 | Matrix-CI по конфигурационному параметру шаблона | M | CI-пайплайн для шаблонов запускается с матрицей значений ключевого конфигурационного параметра. Немедленно выявляет hardcoded константы, которые не проявляются у автора с дефолтным именем. | active |
| DP.M.034 | ArchGate Operational Backing Check | M | Метод проверки качества ArchGate-профиля ЭМОГССБ: профиль силён, когда backed операционными данными; слаб, когда строится на paper comparison. 3 диагностических признака слабого профиля + финализирующий вопрос. | active |
| DP.M.035 | Явные триггеры извлечения модуля в сервис | M | При выборе 'модуль внутри монолита/Worker' вместо 'отдельный микросервис' — немедленно задокументировать измеримые триггеры обратного extraction. 4 типа триггеров. Без явных триггеров решение становится вечным и пропускает правильный момент для review. | active |
| DP.M.036 | Peer Agent Onboarding | M | — | draft |
| DP.M.037 | Personal Guide Lifecycle | M | — | draft |
| DP.M.038 | Идемпотентное распределение скиллов при рендере | M | Паттерн: при каждом рендере персонального руководства агент идемпотентно копирует набор скиллов в .claude/skills/ целевого репо. Идемпотентность: копировать только при отсутствии файла или изменении checksum. Цель: обеспечить channel-parity — доступность скиллов в browser-канале без VS Code. | active |
| DP.M.039 | Manifest Version Release Gate (Проверка версии manifest перед релизом) | M | Pre-release детектор: версия в manifest.json должна совпадать с версией в CHANGELOG.md. Ловит забытый запуск generate-manifest.sh перед релизом. | draft |
| DP.M.040 | Progress Counter N/M для batch-операций CLI (CLI Batch Progress UX) | M | Вывод прогресс-строки (N/M) в теле batch-цикла в shell-скриптах предотвращает иллюзию зависания при длинных операциях. Порог: >10 итераций или >5 сек. | draft |
| DP.M.041 | Posttooluse Hook Derived Sync | M | — | draft |
| DP.M.042 | Platform Audit Multilens | M | Поэтапная ревизия production-платформы: 12-factor (уровень 0) → SRE/SLO (1) → Well-Architected (2) → Team Topologies (3) → TOGAF (4) → DORA (5) → LLMOps (6) | active |
| DP.M.043 | Жизненный цикл генерируемых артефактов: явный archive-шаг с retention-окном | M | — | active |
| DP.M.044 | Extractor Yesterday Step | M | Extractor Yesterday — паттерн замыкания knowledge pipeline: Day Open явно включает просмотр captures экстрактора за вчера как обязательный шаг до начала новой работы. Без этого шага captures попадают в inbox, но не в фокус сессии. | active |
| DP.M.045 | Три оси Service Clause автоматизированного процесса | M | — | active |
| DP.M.046 | Keyset pagination для projection-worker | M | — | active |
| DP.M.047 | Стресс-тест бэкапа через restore | M | — | active |
| DP.M.048 | Дисциплина scope-решений при закрытии РП | M | Метод определяет, когда смежная работа, обнаруженная при реализации или закрытии РП, должна стать фазой текущего РП, а когда — отдельным РП. Основан на дискриминаторе обещания DP.D.064. | active |
| DP.M.049 | Lean Frontmatter Pilot | M | Двухфазная схема frontmatter / DSL: фаза 1 (пилот) — минимальный набор полей, фаза 2 (после фиксации структуры) — расширение через миграцию в отдельный artifact (concept-graph YAML, schema-registry). Избегает 2-3 переделок за пилот. | active |
| DP.M.050 | Env I Isolation | M | — | active |
| DP.M.051 | Spawned Wp From Phase | M | — | active |
| DP.M.052 | Dt Write Api Browser Channel | M | — | active |
| DP.M.053 | Pack как SoT нормативов: код = зеркало | M | — | active |
| DP.M.054 | Targeted backfill via dedicated queue for cursor-workers | M | — | active |
| DP.M.055 | Config SoT Triplet (Python source + SQL generator + validator) | M | — | active |
| DP.M.056 | IntegrationGate Applicability Test | M | Тест применимости IntegrationGate-каркаса (Service Clause → сценарии → роль → реализация) за пределы код-сервисов: применим к любому repeatable workflow с явным потребителем и измеримым инвариантом — документационные конвейеры, курсовые пайплайны, процессы публикации | active |
| DP.M.057 | A/B-оценка альтернативного ML-компонента | M | — | active |
| DP.M.058 | Гейт создания нового Pack при доменных кандидатах без дома | M | При knowledge extraction с внешнего источника: универсальные кандидаты → существующие PD-Pack'и сразу; доменные кандидаты без существующего Pack'а — defer all-together как extraction-report до single decision point /pack-new vs /pack-extend. Защищает от fragmentation доменной онтологии по чужим Pack'ам. | active |
| DP.M.059 | Триада артефактов закрытия фазы РП | M | Закрытие фазы РП ≠ закрытие РП ≠ открытие нового РП. Полнота закрытия фазы достигается коммитом из трёх артефактов: (1) inbox-context update с дельтой artifacts фазы; (2) cross-link на смежные РП при наличии триггеров; (3) side-artifact (extraction-report, decision log) при наличии extraction-работы. Тест полноты — обратимость через 6 месяцев. | active |
| DP.M.060 | Атомарные ВДВ-шаги | M | — | active |
| DP.M.061 | Детекция bottleneck-shift после устранения tech-блокера | M | После устранения tech-блокера bottleneck не исчезает, а смещается в operational/usage/поведенческий слой. Без переоценки карты направлений рисуют «зелёное» при низком conversion в целевое поведение. Тест: «N дней после снятия блокера — какие пилоты/users изменили поведение?» Если <50% — новый bottleneck в operational/usage, не tech. Анти-паттерн: продолжать наращивать tech-функционал когда operational gap не закрыт (инфляция Inventory без Throughput). | active |
| DP.M.062 | Bridge-backfill через shared identifier при blocked identity-provider | M | При cross-system identity-миграции, когда new identity-provider (ORY, OAuth, SSO) недоступен или unblocked-deploy откладывается — не блокировать миграцию полностью. Искать существующий shared identifier (id, present в обеих БД: legacy + new) и проводить linking через него. Покрытие partial + weekly retry для непокрытых. Тест: «есть ли поле, присутствующее в обеих системах?» Да → backfill через него. | active |
| DP.M.063 | Triple-deploy + URL-derived basename для tool promotion | M | Инструмент, работающий в авторском IWE + FMT-шаблоне (для других пилотов) + DS-репо — требует 3-х синхронизированных копий. Pattern: (1) одна реализация (Python, не bash), (2) три target-локации с симметричными именами, (3) FMT-версия обезличена через `_repo_basename` из git remote URL вместо hardcoded имени. Тест обезличивания: «если установить шаблон в репо с другим именем — скрипт сам подхватит правильное basename?» Да → корректное обезличивание. | active |
| DP.M.064 | Manual smoke + analogous-pattern coverage как substitute полной автоматизации | M | Когда full-automation smoke заблокирован внешним фактором (scheduling, deploy infrastructure, vendor bug) — DoD фазы можно закрыть не пустым deferral, а зачётом manual smoke + analogous-pattern coverage. Тест применимости: «можно ли доказать, что execution-path работает, через два независимых способа использования, оба не зависящие от заблокированного компонента?» Да → architecture validation done, automation defer как отдельная фаза. | active |
| DP.M.065 | 4 условия легитимации temporal-derivation routing | M | Routing через изменяемую Карту (routing_key → path) — temporal fallback, по умолчанию FAIL conjunctive screening ЭМОГССБ по Стабильности. НО: при выполнении всех 4 условий одновременно паттерн становится допустимым: (1) нет override; (2) total pure derivation (каждый kind → ровно один target, нет default/wildcard); (3) freeze-at-assignment (path материализуется в task при pending→assigned); (4) раздельная Карта от справочника. Если хотя бы одно не выполнено → temporal fallback → FAIL. | active |
| DP.M.066 | Multi-round verifier с сужающимся scope | M | — | active |
| DP.M.067 | Two-pass review — subagent + self-revisit | M | — | active |
| DP.M.068 | Scope-creep corrective quad — 4 действия в один fix-pass | M | — | active |
| DP.M.069 | Multi-scenario Service Clause — одно обещание, N delivery-сценариев | M | — | active |
| DP.M.070 | Двухфазный тест гипотезы (baseline → parameterized) | M | — | active |
| DP.M.071 | Pre-implementation smoke | M | — | active |
| DP.M.072 | Split-transaction для late-webhook с CHECK constraint | M | — | active |
| DP.M.073 | Pause-before-fix для воркеров с downstream notifications | M | — | active |
| DP.M.074 | Provisional payment_id для late-binding payment APIs | M | — | active |
| DP.M.075 | No-op heartbeat для детекции silent-fail в scheduled workflow | M | — | active |
| DP.M.076 | Migration flag (default WARN → opt-in FAIL) для постепенной валидации | M | — | active |
| DP.M.077 | Common-prefix compression в output путей и циклов | M | — | active |
| DP.M.078 | Многоточечная propagation нового архитектурного правила | M | — | active |
| DP.M.079 | Pack-watcher cross-repo trigger | M | Push-trigger из Pack-репо (SoT) в downstream-репо через GitHub Actions repository_dispatch. Заменяет polling-cron на push-on-change. Применим к Pack→curriculum, Pack→personal-guide regen, Pack→reward_rules sync. | emerging |
| DP.M.080 | Composite indicator — взвешенная сумма провайдеров | M | — | active |
| DP.M.081 | PII Gate через синтетику — bypass для research-фаз | M | — | active |
| DP.M.082 | WP scope boundary через DP.SC interfaces | M | — | active |
| DP.M.083 | Batch frontmatter enum-validator (pre-commit) | M | — | active |
| DP.M.084 | Batch-extraction pipeline из большого корпуса | M | — | active |
| DP.M.085 | Онбординг пилота: Персональное руководство | M | — | active |
| DP.M.086 | Cheap idempotency: dedicated notification_log вместо ALTER TABLE column | M | — | active |
| DP.M.087 | SECRETS.md как обязательный артефакт перед deploy на новый хост | M | — | active |
| DP.M.088 | CI + pre-commit как defense-in-depth для Pack-инвариантов | M | Двухуровневая защита Pack-инвариантов: pre-commit hook = быстрый локальный fail; GitHub Action = серверный enforcement при push/PR. Агентские коммиты (--no-verify, headless) покрываются только CI-слоем. | active |
| DP.M.089 | Ф0-исследование cost baseline перед LLM-оптимизацией | M | — | draft |
| DP.M.090 | Mutation Testing для CI Enforcement Guards в Pack-репо | M | — | draft |
| DP.M.091 | Scope Guard — enforcement Parliament-модели через enum + schema isolation | M | — | active |
| DP.M.092 | Infra Artifact As Create Flow Step | M | — | active |
| DP.M.093 | CI артефакт встраивается в create-flow, не отдельная задача | M | — | active |
| DP.M.094 | Dual-signal enforcement gate для ритуального перехода | M | — | active |
| DP.M.095 | Atomic cross-repo terminology sync | M | — | active |
| DP.M.096 | Выбор Property Graph vs Triple Store для доменной knowledge base с rich metadata | M | — | draft |
| DP.M.097 | Completeness Gate: cross-check spec-множества vs impl-множества для детекции пропущенных случаев | M | — | draft |
| DP.M.098 | Premise pain probe перед архитектурой автоматизации | M | — | draft |
| DP.M.099 | Illustration as First-Class Pack Object | M | — | — |
| DP.M.100 | Vocabulary Sufficiency Gate | M | — | — |
| DP.M.101 | Семантическое версионирование для Docs-as-Code | M | Алгоритм автоматической классификации bump'ов для docs-as-code: git log от последнего тега → классификация коммитов по паттернам (feat→minor, fix→patch, BREAKING→major) → changelog entry + релиз. Применимо к любому документационному репо с conventional commits. | active |
| DP.M.102 | Условный автоматический merge через метки PR и CI-гейт | M | PR с разрешённой меткой (hotfix, pilot-approved) + все CI-чеки зелёные → автоматический merge. Создаёт ускоренную полосу для срочных исправлений без обхода CI. Граница безопасности: только разрешённые labels + CI pass обязателен. | active |
| DP.M.103 | Жизненный цикл создания доменного Pack (7 фаз) | M | Полный lifecycle создания нового Pack: Ф1 (онтология + SOTA) → Ф2 (различения) → Ф3.5 (extraction из корпуса) → Ф4 (IntegrationGate) → Ф5 (batch mining) → Ф7 (MAP + CHANGELOG + README + SPF 09-11). IntegrationGate до extraction = правильный порядок. SPF 09-11 = обязательное завершение. | active |
| DP.M.104 | Cross-repo publication pipeline via workflow_dispatch + PR gate | M | Человеко-инициируемый кросс-репо pipeline: content-repo → publication-repo через параметризованный workflow_dispatch (guide_id, version) → генерация артефактов по шаблону → gh pr create в целевом репо. PR-гейт обеспечивает editorial review перед слиянием в публичное дерево. Применим для любого паттерна «источник контента → публичная витрина». | emerging |
| DP.M.105 | workflow_call orchestration: единый entry point с разделёнными concerns в CI/CD | M | — | active |
| DP.M.106 | Literature crosscheck при именовании Pack-сущностей | M | При создании новой роли/концепции/метода в Pack — обязательный прогон через 3-4 канонических литературных источника области, выбор имени closest-to-canon вместо собственного. Защищает от re-naming через 3-6 месяцев. | active |
| DP.M.107 | Role Rename Downstream Review | M | — | active |
| DP.M.108 | Specializes Vs Parallel Roles | M | — | active |
| DP.M.109 | Метод операциональной точности интеграционных терминов | M | — | active |
| DP.M.110 | Декларативный словарь предикатов для nudge-движка | M | — | — |
| DP.M.111 | Majority-vote детектор структурного drift | M | — | — |
| DP.M.112 | run_skill() — headless dispatch скиллов через claude -p | M | — | draft |
| DP.M.113 | Разделение earned_total и points в gamification схеме | M | — | draft |
| DP.M.114 | Исторический cap бонусов: интеграл по истории квалификации | M | — | draft |
| DP.M.115 | Конвейер руководств из Pack (Living Documentation CI/CD) | M | Pack = единый источник истины для N руководств. Изменение в Pack → автоматическая валидация структуры → оценка качества → сборка нового контента. Персонализация = дополнительный слой: разделы выбираются по ступени + bottleneck + домен пользователя. | active |
| DP.M.116 | Решение о распределении captures по Pack (Вариант B > Вариант A) | M | При KE из нового источника: предпочтительно распределить по существующим Pack (Вариант B), а не создавать новый Pack (Вариант A). Вариант A оправдан только при: (1) принципиально новый домен, или (2) ≥30% сущностей не вписываются ни в один существующий Pack. | active |
| DP.M.117 | Cohort Content As Declarative Json | M | — | active |
| DP.M.118 | Cohort Intake Survey Freeze | M | — | active |
| DP.M.120 | Boundary Mapping Constant — single source граничного маппинга | M | — | active |
| DP.M.121 | Universal Guide Phases F0 F6 | M | — | draft |
| DP.M.122 | Security Culture (Pilot habits) | M | — | draft |
| DP.M.123 | Backup (Pilot method) | M | — | draft |
| DP.M.124 | Encryption (Pilot method) | M | — | draft |
| DP.M.137 | Auto-Trigger Subagent Review on First Subsection | M | — | active |
| DP.M.138 | Dispatcher: синхронизация origin и идемпотентная запись результата после headless-агента | M | — | draft |
| DP.M.139 | Lint-плейсхолдер как детектор онтологических пробелов Pack | M | — | draft |
| DP.M.140 | Двухфазный жизненный цикл онтологических терминов: forming → formalized | M | — | draft |
| DP.M.141 | Выбор source в pack_refs: ID Pack vs docs + ontology_anchor | M | — | — |
| DP.M.142 | CI Setup Flag Mode Separation | M | — | draft |
| DP.M.145 | Terminology replace — multi-pass verify through peer agent | M | — | draft |
| DP.M.146 | Working-hypothesis marker with verification source | M | — | — |
| DP.M.147 | Semantic-first / Performance-later layered integration | M | — | draft |
| DP.M.148 | Audit cascade — обновление главного документа с прогоном связанных на drift | M | — | — |
| DP.M.149 | Bearer == Shared Secret Backward-Compatible Auth Mode | M | — | draft |
| DP.M.150 | Multi-Driver Compat via Duck-Typing of Connection API | M | — | draft |
| DP.M.153 | Scaffold Fallback — Minimal Valid Document (не пустой файл) | M | Else-ветка guard-блока в cascaded scaffold-системе создаёт минимальный валидный документ (frontmatter + комментарий generated_by: fallback), а не пустой файл через touch. Downstream-парсеры получают рабочую оболочку, а не падают на отсутствующем YAML-блоке. | draft |
| DP.M.154 | Embedded Python в bash — обязательные with-блоки (CPython-refcount-independence) | M | Embedded-Python сниппет в shell-скрипте для write-операций над manifest/config/state-файлами обязан использовать `with open(...) as f:` для каждого open. Безсонтекстный `json.dump(d, open(f, 'w'))` зависит от CPython refcount-driven __del__ — рискует partial-write на async/PyPy/exception. | draft |
| DP.M.155 | Raw GitHub Distribution Model (raw-main delivery — коммит в main = production, version — info label не gate) | M | Модель доставки template-системы через raw.githubusercontent.com/<owner>/<repo>/main/<path>. Любой коммит в main немедленно доступен пользователям при следующем update.sh. Версия в manifest — информационная метка, не gate. Цена: pre-merge CI становится единственным защитным барьером. | draft |
| DP.M.156 | Upgrade-Markers в Service Contract | M | — | — |
| DP.M.157 | CI-чек покрытия манифеста дистрибутива | M | — | — |
| DP.M.158 | Archgate Defer Pattern | M | — | — |
| DP.M.159 | Скилл как единственная исполняемая точка входа | M | — | — |
| DP.M.160 | Single point of degradation tracking | M | — | active |
| DP.M.161 | Pack-зрелость как параметр оценки трудозатрат | M | — | active |
| DP.M.162 | Adversarial Peer Review для методологических текстов | M | — | draft |
| DP.M.163 | Checkpoint-протокол для отложенной финализации фазы РП | M | — | draft |
| DP.M.164 | Base Group Replaces Domain Multiplier | M | Замена двойного кодирования ценности (domain_mult × base_group) на единственный base_group. Домен остаётся аналитическим атрибутом, не множителем в формуле начисления очков. | active |
| DP.M.165 | Soft streak reset — плавное снижение вместо обнуления | M | — | active |
| DP.M.166 | Referral-вознаграждение через ₽-кредит, не баллы | M | — | active |
| DP.M.167 | Ветвление refinement-промпта по длине предыдущего ответа | M | — | — |
| DP.M.168 | Post-deploy регрессия как гипотеза №1 в RCA | M | — | — |
| DP.M.169 | Экспериментальный вес с guard-условием для ML-метрики | M | — | active |
| DP.M.170 | Router-роль как рычаг разделения dispatch-решения от исполнения | M | — | active |
| DP.M.171 | Fpf Sync Delta Map | M | — | active |
| DP.M.172 | Knowledge File Archive Vs Delete | M | — | active |
| DP.M.173 | Artifact-first контракт agentic-роли с confidence-полем | M | — | — |
| DP.M.174 | Triple-hash idempotency для LLM-pipeline | M | — | — |
| DP.M.176 | WP Inbox: flat-file vs folder structuring | M | — | — |
| DP.M.177 | Управление жизненным циклом bug-report в inbox | M | Метод управляет жизненным циклом bug-report файлов в inbox/bugs/ через frontmatter-статус (open|resolved|invalid) и триггер Week Close: автоматический review открытых багов старше 14 дней с архивацией разрешённых. | active |
| DP.M.178 | Wp Triage Three Step Filter | M | — | active |
| DP.M.179 | Single Source Dashboard Script | M | — | active |
| DP.M.180 | Defer Policy No Auto Escalate | M | — | active |
| DP.M.181 | Multi Turn Session Thread Pattern | M | — | draft |
| DP.M.182 | Dual Sla Acknowledgment Completion | M | — | draft |
| DP.M.183 | Level Dependent Bonus Caps Ema | M | — | — |
| DP.M.184 | EMA-сглаживание курса бонусов | M | — | — |
| DP.M.185 | Степенная функция начисления баллов за усилие | M | — | — |
| DP.M.186 | Тест 15-секундного обещания onboarding | M | — | — |
| DP.M.187 | Бустер новичка: фиксированный множитель первые N дней | M | — | — |
| DP.M.188 | Маппинг N backend ступеней в M UI грейдов | M | — | — |
| DP.M.189 | Floor курса для защиты бизнес-обещания при росте community | M | — | — |
| DP.M.190 | 3-уровневый fallback для технического риска в live-демо | M | — | — |
| DP.M.191 | CTA воронки = ближайший продукт по времени, не самый ценный | M | — | — |
| DP.M.192 | C9-проверка: абстрактный термин → сцена с человеком в действии | M | — | — |
| DP.M.193 | Гибридный фикс — regex tolerance + локальная унификация | M | — | draft |
| DP.M.194 | Anchored regex для frontmatter-aware матчинга | M | — | draft |
| DP.M.195 | Pull-driven feature activation — defer до explicit user request | M | — | active |
| DP.M.196 | Upsert Runtime Verify Double Delta | M | — | proposed |
| DP.M.197 | Fix Contract (FC) — исполняемая спецификация исправления с regression_checks | M | — | — |
| DP.M.198 | Атомарный переход в degrade-state: state + user-reply одним PUT | M | — | — |
| DP.M.199 | Три уровня параметров конфигурируемой системы | M | — | active |
| DP.M.200 | Самофинансирующийся реферальный механизм | M | — | active |
| DP.M.201 | Separate API Keys per Workload (изоляция квот по рабочим нагрузкам) | M | — | active |
| DP.M.202 | Loyalty: отдельная группа community events с двумя независимыми лимитами | M | — | active |
| DP.M.203 | Neon multi-DB FDW cross-schema prefix rules | M | — | — |
| DP.M.205 | Gamification Rate Limit by Event Controllability | M | — | — |
| DP.M.206 | Fast-fail-and-restart предпочтительнее in-process reconnect когда состояние коннекта = source-of-truth подписки | M | — | active |
| DP.M.207 | Explicit choice до stateful default при первом входе | M | — | active |
| DP.M.208 | Diagnostics до behavioral nudge при stuck-сегменте | M | — | active |
| DP.M.209 | Dry-run = 50% production migration: полный checklist с явным блокером | M | — | active |
| DP.M.210 | Трёхуровневая сегментация застрявших пользователей (α/β/γ) для диагностики bottleneck | M | — | active |
| DP.M.211 | Диагностика L1 FAIL в concept-coverage по регистрационному зазору | M | — | active |
| DP.M.212 | Маппинг Discourse webhook в IWE event pipeline | M | — | active |
| DP.M.213 | UPSERT + xmax=0 — атомарное определение INSERT vs UPDATE | M | — | active |
| DP.M.214 | Silent OAuth Token Provisioning — провиженинг через session cookie | M | — | active |
| DP.M.215 | SQL NOT EXISTS guard для predicate-based row exclusion | M | — | active |
| DP.M.217 | Glue Requires Executor Pipeline Decomposition | M | — | active |
| DP.M.218 | Defense-in-depth протокола: Close-check + Open-autofix | M | — | active |
| DP.M.219 | BY-SCRIPT маркер — идемпотентная авто-инжекция в шаблонный файл | M | — | active |
| DP.M.220 | Threshold-or-time авто-коммит с daily squash | M | — | active |
| DP.M.223 | Marp тёмная тема — layout-классы для структурированных презентаций | M | — | — |
| DP.M.225 | Identity-anchor персонаж в семинаре | M | — | draft |
| DP.M.226 | Прогрессивное заполнение карточки в семинаре (3 точки) | M | — | draft |
| DP.M.230 | Двухуровневая защита async replay-loop от infinite retry (outer + per-event wait_for) | M | — | active |
| DP.M.231 | Одновременное восстановление N domain-rules как диагностический маркер блокировки main loop | M | — | active |
| DP.M.232 | Декомпозиция umbrella-РП: domain-specific subsystem ≠ standard infra direction | M | — | active |
| DP.M.233 | Cutover-date в детекторе вместо backfill legacy state | M | — | active |
| DP.M.234 | Двухусловное определение «открыто» для гигиены workflow-артефактов | M | — | active |
| DP.M.235 | Audit зонтичного РП: rescope через promote/cancel/defer/spawn | M | — | active |
| DP.M.236 | Разделение фазы РП по классу верификации (trivial/closed-loop/open-loop/problem-framing) | M | — | draft |
| DP.M.237 | Auto-route первого входа + explicit manual override affordance (SRB pattern) | M | — | draft |
| DP.M.238 | Pre-articulated open questions в отложенной problem-framing фазе | M | — | draft |
| DP.M.239 | Defense-in-depth bail-out при refactor regex single→multi: fail-loud вместо silent best-effort | M | — | active |
| DP.M.240 | Self-recoverable tooling: SoT в репо + symlink/copy в writable PATH | M | — | active |
| DP.M.241 | Порядок формирования персонального руководства | M | — | active |
| DP.M.242 | Ar5 Pack Quality Baseline | M | — | accepted |
| DP.M.243 | Discriminator Column Sti Pattern | M | — | — |
| DP.M.244 | Trust Boundary Server Side Authz | M | — | — |
| DP.M.245 | Cp Profile Adaptive Facilitation | M | — | — |
| DP.M.246 | Content Debt Triage Inbox | M | — | — |
| DP.M.247 | Pre-LLM Eligibility Gate | M | — | active |
| DP.M.248 | Composable CLI Linter — One Subcommand per Rule | M | — | active |
| DP.M.249 | Delivery Tracker — Living Navigation Artifact for Umbrella WP | M | — | active |
| DP.M.250 | Glossary-Driven Lint via YAML — Rules as Data | M | — | active |
| DP.M.251 | Nighttime Rollout with Pre-Deploy Rollback and Post-Deploy Verifier | M | — | active |
| DP.M.252 | Satisfied-by-Existing-Content — pre-build scout как класс defer в delivery pipeline | M | — | active |
| DP.M.253 | Seminar Orientation Map — max-impact triple для семинара с концептуальным контентом | M | — | active |
| DP.M.254 | Container abstraction mapping — IT-аналогии через Persona+Память+Контекст без импорта docker-терминов | M | — | active |
| DP.M.255 | Поликорневая сборка контекста | M | — | active |
| DP.M.256 | Pointer Only Fork Closure | M | — | active |
| DP.M.257 | Closed Partial Multi Channel Resumption | M | — | active |
| DP.M.258 | Cross Component Trigger Body Search Path | M | — | — |
| DP.M.259 | Resource constraint доминирует в портфеле при одном исполнителе | M | — | active |
| DP.M.260 | Intentional disablement как третья гипотеза при пустой/нулевой функции | M | — | active |
| DP.M.261 | Port working SQL из known-good источника vs реимплементация | M | — | active |
| DP.M.262 | Bidirectional cross-reference как защита от lifecycle coupling через чужой exec-механизм | M | — | active |
| DP.M.263 | Каскад Pack-расширения через ad-hoc → snapshot → audit → авто-WP | M | — | current |
| DP.M.264 | Пороговый сценарий аудита вместо отдельной операционной роли | M | — | current |
| DP.M.265 | Delta Signal Not Raw Values | M | — | active |
| DP.M.266 | Internal service auth: shared secret + X-User-ID header вместо user_jwt propagation | M | — | active |
| DP.M.267 | Grep Marker Deferred Auto Registry | M | — | — |
| DP.M.268 | Auto Generated Ownership Marker | M | — | — |
| DP.M.269 | Bidirectional Registry Drift Guard | M | — | — |
| DP.M.270 | Resolve Instructions Level | M | — | — |
| DP.M.271 | Lazy Channel Aware Resource Creation | M | — | — |
| DP.M.272 | Role Unpacking Via Split To | M | — | — |
| DP.M.273 | Explicit Prefix Guard Disambiguation | M | — | — |
| DP.M.274 | Три уровня мастерства пилота (Iron Man framing) | M | — | active |
| DP.M.275 | Sc Decomposition Via Umbrella | M | — | — |
| DP.M.276 | Add Not Rename On Unpacking | M | — | — |
| DP.M.277 | Single Source Method N Surfaces | M | — | — |
| DP.M.278 | Hybrid Corpus Audit Protocol | M | — | — |
| DP.M.279 | Held Patch Pattern | M | — | — |
| DP.M.280 | Allow Fallback Cutover Pattern | M | — | — |
| DP.M.281 | Recurring Error Diagnosis | M | — | active |
| DP.M.282 | Function First Onboarding | M | — | active |
| DP.M.283 | Byok First Tier Unlock | M | — | active |
| DP.M.284 | Inline Cat Over Add Dir Cli | M | — | — |
| DP.M.285 | Dual Write Safety Net Projection Migration | M | — | — |
| DP.M.286 | Cold Review Frontmatter Anchors Pass | M | — | — |
| DP.M.287 | Grace Window Overlapping Scheduled Jobs | M | — | — |
| DP.M.288 | Dual-nudge same-day re-engagement — два нуджа о практике в день доставки контента | M | — | active |
| DP.M.290 | Explicit next-step numbering — явный номер следующего шага вместо абстрактного "завтра" | M | — | active |
| DP.M.291 | Patch Object Vs String Path Mock | M | — | — |
| DP.M.292 | Tier Source Provenance | M | — | — |
| DP.M.293 | Graceful Degradation Secondary Db Timeout | M | — | — |
| DP.M.294 | Extraction Report Lifecycle Applied Archive | M | — | — |
| DP.M.295 | Digital Twin Derived Over Primitive | M | — | — |
| DP.M.296 | Diagnosis Drill Down All Weak Slices | M | — | — |
| DP.M.297 | Platform Specific Path From Params Yaml | M | — | — |
| DP.M.298 | Fail-closed scope sidecar: ранний парсинг + deny при недоступности сервиса | M | — | — |
| DP.M.299 | Rotation impact map: инвентаризация мест секрета до ротации | M | — | — |
| DP.M.300 | gh pr diff branch-on-branch: проверка реального scope PR через checkout | M | gh pr diff на ветке поверх feature-ветки показывает изменения обеих суммарно; реальный scope PR берётся через checkout + git log main..HEAD. | — |
| DP.M.301 | Sync source-of-truth → derived: edit-commit-push в SoT, derived read-only | M | Две копии одного файла, синхронизируемые односторонне: правки только в источнике через commit перед sync, производная read-only — иначе sync затирает правки незакоммиченным состоянием. | — |
| DP.M.302 | Trusted-reference хранилище: immutable контракт + audit-таблица рядом | M | — | draft |
| DP.M.303 | Production DDL через gated-шаг: отдельный .sql файл вне application code | M | — | draft |
| DP.M.304 | Локальный импорт тяжёлой зависимости для optional backend | M | — | draft |
| DP.M.305 | Frozen formula hash: версия формулы как артефакт под change-control | M | — | draft |
| DP.M.306 | Честная деградация тайла панели: статусные коды вместо дефолтных значений | M | — | draft |
| DP.M.307 | Bootstrap mode метрики по N выборки: hidden / interval / point | M | — | draft |
| DP.M.308 | Reader Contract Check Before Gate Removal | M | — | active |
| DP.M.309 | Halliday Language Rule Routing | M | — | active |
| DP.M.310 | Три измерения консистентности при автопереводе | M | Автоперевод требует трёх ортогональных измерений: (1) текст (LLM translation), (2) граф понятий (термин-глоссарий), (3) стиль (per-language style base). Пропуск любого — специфический вид drift. | — |
| DP.M.311 | File-fallback из шаблона: graceful degradation при отсутствии Pack | M | Интерфейс-слой (FMT-шаблон) доставляет базовое поведение (стили, правила, шаблоны) пользователю двухуровневой цепочкой: сначала ищем в Pack (полный домен), при отсутствии — берём встроенный фолбэк из шаблона. Необязательный Pack перестаёт быть жёсткой зависимостью. | — |
| DP.M.312 | OAuth prompt=login: принудительная re-authentication через стандартный параметр | M | Если клиент держит refresh-токен/grant и при reconnect не показывает форму входа, добавление параметра prompt=login (RFC 6749) к OAuth-authorize URL заставляет identity-провайдер игнорировать существующую сессию и потребовать свежую аутентификацию. | — |
| DP.M.313 | Enforcement ladder: уровни и критерии promotion правил | M | Каждое правило системы существует на одном из 5 уровней enforcement (E0 существует — E4 блокирует merge). Промоция между уровнями — отдельная инженерная задача. Главный паттерн — E3 (ручное ревью) → E2 (CI-скрипт) при условии однозначной автоматической проверки. | — |
| DP.M.314 | Structural criterion over symbol heuristic | M | При дизайне lint/audit-скриптов для markdown-артефактов проверять AST-структуру (заголовки уровней + непустые блоки), а не символьные паттерны (пунктуация →, :, *). Символьные эвристики дают false-positive на заголовках и false-negative на альтернативных нотациях. | — |
| DP.M.325 | Radar Analog Search Before Build | M | — | draft |
| DP.M.326 | Crystallization Threshold | M | — | draft |
| DP.M.327 | Multi Level Lookup Diagnostic Precision | M | — | draft |
| DP.M.328 | Yaml Preload Pure Bash Lookup | M | — | draft |
| DP.M.329 | Идемпотентность вебхука на уровне ограничения БД (ON CONFLICT DO NOTHING) | M | — | active |
| DP.M.331 | Agent Audit Trail as Append-only Sidecar | M | Машиночитаемый журнал действий агента хранится как отдельный append-only файл (audit-<id>.jsonl) рядом с человекочитаемым тредом сессии. Записывает события, которых нет в треде: вызовы инструментов, чтение/запись файлов, коммиты. Включается в коммит хода → переживает git reset --hard. | — |
| DP.M.332 | Sanity-guard quarantine pattern | M | Guard срабатывает двухслойно: (1) аномальная запись получает статус карантина, не финальный статус; (2) каждое срабатывание оставляет durable-след в инцидент-таблице. Уведомление эфемерно, инцидент-запись — нет. Решение о финализации отделено от детекции. | — |
| DP.M.333 | Failure mode matrix per event type | M | Явная таблица 'тип события → режим отказа' как артефакт дизайна до реализации. Юридически значимые события (согласие) при сбое инфраструктуры принимаются в очередь и дозаписываются; привилегии честно ждут. Один дефолт для всей системы = архитектурная ошибка. | — |
| DP.M.334 | Commented-out code with explanation as primary evidence of intentional disablement | M | При расследовании silent data gap первый поиск — git log --pickaxe + grep закомментированных строк с объяснением в планировщиках и main entry points. Закомментированный код с явным объяснением = primary evidence того, что компонент намеренно остановлен при миграции. | — |
| DP.M.335 | Adversarial Layered Review for Security-Critical Components | M | Для security-critical компонентов первое «полное» решение — baseline для adversarial review, не финал. Peer последовательно ищет attack surface в принятом fix; каждый новый fix открывает следующую поверхность. 2-3 раунда существенно меняют архитектуру решения. | — |
| DP.M.339 | Break-glass Key Distribution via Agent | M | При создании аварийного (break-glass) ключа агент играет роль генератора и передатчика: приватная часть показывается пилоту один раз, локальная копия удаляется. Хранение break-glass ключа — исключительно за человеком. Агент генерирует автоматический ключ и закрытый аварийный ключ в одной операции, но логика хранения у них разная. | — |
| DP.M.340 | Skill Resource Guard: Open-first, Close-last | M | Любой скилл, изменяющий файлы, открывает ресурс-гуард как первый шаг (до первого Write/Edit в сессии) и закрывает как последний шаг (после push, best-effort). Паттерн предотвращает конкурентный доступ нескольких агентов к одним и тем же файлам в мульти-агентной среде. | — |
| DP.M.341 | Verify Existing Security Pattern Before Implement | M | — | — |
| DP.M.342 | Grant Execute Not Direct Table Access Via Security Definer | M | — | — |
| DP.M.346 | Dual Lens Publication Verification | M | Два параллельных субагента разной природы (конформность фреймворку / адверсарный SOTA-поиск) перед публикацией — диверсификация линз ловит разные классы ошибок, которые одна проверка не обнаруживает. | — |
| DP.M.347 | Portrait First Reference Fallback | M | Рендер персонализированного артефакта: сначала читается пользовательский контекст (portrait), при отсутствии — graceful fallback на общий справочник. Интерфейс результата одинаков независимо от источника. | — |
| DP.MAP.001 | Pack Navigation Map | MAP | — | — |
| DP.MAP.002 | IWE Service Catalog | MAP | Кросс-системный каталог всех сервисов IWE: сервис → роль → вход → выход → потребитель → исполнитель → триггер | draft |
| DP.METHOD.010 | Kinds + Owner Roles | METHOD | Формальная процедура старта онтологической работы: сначала определить Kinds (типы сущностей) и Owner Roles (кто source-of-truth), только потом выравнивать лексику. Предотвращает DP.FM.012. | active |
| DP.METHOD.020 | Траектория развития Созидателя | METHOD | Как строить траекторию персонального развития через 5 ролей Созидателя, ступени и степень квалификации. Для Навигатора, Портного и системы персональных руководств. | active |
| DP.METHOD.030 | Метод перевода терминов IWE | METHOD | Воспроизводимый алгоритм выбора name_en для Pack-понятия с RU-каноном и обратно. EN — pivot-язык для последующих переводов. | active |
| DP.METHOD.031 | Метод онтологического сопоставления Pack-понятий с FPF-корнями | METHOD | Алгоритм назначения FPF-корня (U.*) для нового Pack-понятия. Предотвращает изолированные понятия и silent drop рёбер при индексации. | active |
| DP.METHOD.040 | Метод ER-моделирования | METHOD | Правила построения концептуальных ER-диаграмм: сущности физ.мира, связи между ними, трансформация в физическую схему РСУБД. Применяется при проектировании новых БД и при ревизии существующих. | active |
| DP.METHOD.041 | Метод связывания доменных сущностей с физ.реализацией | METHOD | Правило связывания доменных сущностей Pack (DP.D.*, DP.CONCEPT.*) с физ.реализацией (таблица БД в DP.ARCH.004 §10) и кодовой реализацией (DS-файлы/модули). Сохраняет OwnerIntegrity: один факт — одно место, обратная ссылка из Pack в реализацию есть, но источник правды — DP.ARCH.004. v2 (24 апр, WP-228 Ф30) расширен §4 ARCH-bump sync-процессом и §5 антипаттерном дублирования формулировок downstream. | active |
| DP.METHOD.042 | Сценарии использования concept-графа агентами в runtime | METHOD | 4 сценария применения concept-графа агентами платформы IWE: Claude Code (я), автор Pack, ролевые агенты бота (Портной/Оценщик/Навигатор), учебная траектория. Каждый описан по шаблону IntegrationGate шаг 2: потребитель → триггер → запрос → использование → observable-сигнал. | draft |
| DP.METHOD.050 | Метод применения Quantum-Like Lens (QL-lite) | METHOD | Дисциплина применения quantum-like линзы FPF C.26* в проектировании метрик, диагностики, наблюдаемости и архитектурных решений. Активируется только при остаточной запутанности после классического набора. Включает 5 предохранителей и явный критерий выхода. | active |
| DP.METHOD.051 | n8n встроенный /healthz endpoint для внешнего мониторинга | METHOD | — | — |
| DP.METHOD.053 | Метод извлечения НЭП (Неудовлетворённость / Эмоция / Проблема) | METHOD | Сократически-структурированный разбор сырых заметок и рефлексии на триаду Проблема / Неудовлетворённость / Эмоция с привязкой к роли и силе, выводящий пилота к целям и приоритетам месяца. Единый источник (single-source структуры) для обоих каналов discovery R1 Стратега — локального skill и серверного multi-turn. | active |
| DP.METHOD.054 | Метод кодирования и классификации IWE | METHOD | Метод заведения, ведения, версионирования и отмены кодов и схем классификации (реестров, нумераций, префиксов) в IWE. Объединяет две дорожки: дизайн схемы (корректность — фасеты, владелец namespace, разделение ID и классификации) и принуждение схемы (выживание — ось механизмов E0-E3). Предиктор выживания схемы — стоимость нарушения, не качество дизайна; но хорошо принуждённая плохая схема остаётся плохой, поэтому обе дорожки обязательны. | active |
| DP.METHOD.055 | Метод безопасного автоматического git push (push-invariant) | METHOD | Тройное условие перед автоматическим push в CI/CD или cron: clean tree AND ahead>0 AND behind==0. При behind>0 (diverged) — молча пропустить, сигнализирует pull-alert компонент. | active |
| DP.METHOD.056 | Pre-deploy аудит потребителей перед сменой имён MCP-инструментов | METHOD | Перед деплоем gateway с изменёнными именами инструментов — grep по всем клиентским репо на старые имена. Деплой только атомарно с обновлением потребителей. | active |
| DP.METHOD.057 | Идемпотентные SQL-миграции | METHOD | Миграция БД должна быть безопасна при повторном запуске: проверка «уже существует» перед созданием, проверка «ещё существует» перед удалением. | active |
| DP.METHOD.058 | Повтор и форк сессии агента | METHOD | Восстановить контекст агентской сессии до выбранной точки решения, чтобы исследовать альтернативный путь (форк) или воспроизвести рассуждение (повтор). | active |
| DP.METHOD.059 | Bash 32 Portability Python3 Heredoc | METHOD | — | draft |
| DP.METHOD.060 | Skill Promotion L2 To L1 | METHOD | — | draft |
| DP.METHOD.061 | Incremental Architecture Seed Order | METHOD | — | draft |
| DP.METHOD.062 | Skill Description Scope Guard | METHOD | — | draft |
| DP.METHOD.063 | Wp To Pack Migration Flow | METHOD | WP→Pack migration flow: WP-document = thinking workspace (mutable), Pack = canonical source of truth (stable). After crystallization — content migrates to Pack, WP moves to archive. | draft |
| DP.METHOD.064 | Outcome Gate Pending Status | METHOD | gate:outcome-pending — formal interim phase status between 'mechanism verified' and 'prod behaviour confirmed'. Prevents premature phase closure when tests pass but production observation period not yet complete. | draft |
| DP.METHOD.065 | Verifier Before Assembly | METHOD | Verifier-before-assembly: explicit source availability check before content generation. Returns missing_source:<name> flags instead of silently falling back to defaults. | draft |
| DP.METHOD.066 | Probe First | METHOD | Probe-first: read-only разведка live-системы ДО реализации фазы, чья карточка делает проверяемые фактические утверждения о системе. | draft |
| DP.METHOD.067 | Honest Provenance Backfill | METHOD | При backfill provenance-колонки для данных неустановленного происхождения: 'unknown'+'flagged' (очередь аудита), не правдоподобный-но-непроверенный источник. | draft |
| DP.METHOD.068 | Denormalize Provenance Column | METHOD | При хранении провенанс-ссылки в сущности: денормализовать reference-колонку прямо в сущность, а не создавать отдельную canonical-таблицу-справочник — это избегает второго источника истины. | draft |
| DP.METHOD.096 | Phase Absorption By Child Wp | METHOD | При росте дочернего РП в самостоятельный зонтичный — зонтичный-родитель явно передаёт конкретные фазы дочернему и переопределяет свой scope. Смешение scope между РП запрещено (OwnerIntegrity). | draft |
| DP.METHOD.097 | Explicit Date Arg Reproducibility | METHOD | Скрипт-генератор с date-dependent output принимает дату явно как --date YYYY-MM-DD. datetime.now() как единственный источник даты запрещён: делает вывод нетестируемым и ретроактивный рендеринг невозможным. | draft |
| DP.METHOD.098 | Dedicated Style Gate Pipeline | METHOD | Выделенный пост-генерационный gate с детерминированным словарём проверяет стиль/compliance LLM-вывода. Style-инструкция в промпте = 'попросить'; gate = 'проверить'. Оба нужны. | draft |
| DP.METHOD.099 | Local Gateway Render Extension | METHOD | Local Gateway = тонкий MCP-сервер, расширяемый операциями над локальными файлами пилота. Каждое расширение = отдельный Service Clause. Ограничение: онлайн только при работающей машине пилота. | draft |
| DP.METHOD.100 | Testing As Specification | METHOD | Тест задаёт форму ожидаемого выхода — это и есть спецификация. Хороший тест читается как требование: при этом входе — этот выход. Тест без observable assertion (assert True, вечно-зелёный тест) — не тест, а шум. Документация устаревает; тест — вместе с кодом. | draft |
| DP.METHOD.101 | Append Only Audit Journal Integrity | METHOD | Hash-chain trigger + SELECT FOR UPDATE на stream_tip + JCS + OTS anchors = дизайн append-only аудит-журнала с tamper-evidence и serialized writes без гонки хешей. Применимо к любому журналу с требованием non-repudiation. | draft |
| DP.METHOD.102 | Principle Hierarchy Zpf Tpf | METHOD | Иерархия ZPF→FPF→SPF→TPF→LPF классифицирует принципы по специфичности применения. Поиск: сверху вниз (от универсального к инстансу/носителю). Загрузка в агента: снизу вверх (LPF→ZPF), чтобы специфические перевешивали общие при конфликте. | draft |
| DP.METHOD.103 | Language Parametric Onboarding Route | METHOD | — | — |
| DP.METHOD.104 | Lpf Role Substitution Test | METHOD | — | — |
| DP.METHOD.105 | Rule By Function Not Location | METHOD | — | — |
| DP.METHOD.106 | Mutation Test Honesty Check | METHOD | — | — |
| DP.METHOD.107 | Rule By Template Structure | METHOD | — | — |
| DP.METHOD.108 | Error Counter Scale Diagnostic | METHOD | — | — |
| DP.METHOD.109 | Measurement Layer Check Before Data Wait | METHOD | — | — |
| DP.METHOD.110 | Alert Repeat Ack Gate | METHOD | — | — |
| DP.METHOD.111 | Date Failure From First Launch Log Boundary | METHOD | — | — |
| DP.METHOD.112 | Silent Component Triage Necessity First | METHOD | — | — |
| DP.METHOD.113 | Acl Companion Artifact Schema Pipeline | METHOD | — | — |
| DP.METHOD.114 | Diagnostics On Transient Failure | METHOD | — | — |
| DP.METHOD.115 | Storage Writer Diagnosis Via Grants | METHOD | — | — |
| DP.METHOD.116 | Позиционирование user_id в хэш-цепочке при праве на забвение | METHOD | Позиция user_id внутри vs снаружи хэшируемого содержимого — обязательная развилка дизайна для append-only журнала с PII и требованием GDPR right-to-erasure. | active |
| DP.METHOD.117 | Мёртвый форк скрипта: верификация grep + прямое удаление | METHOD | Безопасное устранение расходящейся копии инструмента: grep-верификация отсутствия вызовов + прямое удаление. Wrapper-редирект отклоняется: маскирует ошибку, создаёт ложный легитимный вход. | active |
| DP.METHOD.118 | Peer Dispute First Source Verification | METHOD | — | — |
| DP.METHOD.119 | Watchdog check guard order (последовательные guard-условия) | METHOD | — | active |
| DP.METHOD.120 | Multi-session reconcile gate (явный reconcile-ход при N>2 параллельных сессиях) | METHOD | — | active |
| DP.METHOD.121 | Admin Delete Immutable Log Session Local Bypass | METHOD | Административное удаление записи из immutable append-only журнала: 4-шаговая атомарная транзакция с SESSION_LOCAL bypass триггера только-для-последней-записи. Применимо к любому append-only хранилищу с GDPR-требованиями. | draft |
| DP.METHOD.122 | Month Close Rebuild Strategic Context | METHOD | — | — |
| DP.METHOD.123 | Migration Number Collision As Coordination Signal | METHOD | — | — |
| DP.METHOD.124 | Stateless Windowed Recompute | METHOD | — | — |
| DP.METHOD.125 | Guard Normalized Ratio Not Raw Numerator | METHOD | — | — |
| DP.METHOD.126 | Context Freshness Flag | METHOD | — | — |
| DP.METHOD.127 | Wp Next Step Guide Block | METHOD | — | — |
| DP.METHOD.128 | Detector Selftest Synthetic Regression | METHOD | — | — |
| DP.METHOD.129 | Quarterly Cadence Month Close Mod3 | METHOD | — | — |
| DP.METHOD.130 | Atomic Upsert On Conflict Race Prevention | METHOD | — | — |
| DP.METHOD.134 | Authored File Deferred Conflict Delivery | METHOD | — | — |
| DP.METHOD.135 | Render Checklist Separate Artifact | METHOD | — | — |
| DP.METHOD.136 | Archive Integrity Listing Baseline | METHOD | — | — |
| DP.METHOD.137 | Staged Migration Read Path Deferred Delete | METHOD | — | — |
| DP.METHOD.138 | Knowledge Atom Normal Form Multi Consumer | METHOD | — | — |
| DP.METHOD.139 | Methodology Pilot One Document Before Corpus | METHOD | — | — |
| DP.METHOD.140 | E2E Pipeline Shakedown | METHOD | — | — |
| DP.METHOD.141 | Sota Sheet Lite Before Pack Name | METHOD | — | — |
| DP.NAV.001 | Навигация знаний | NAV | 4-уровневая навигация знаний между репозиториями: FPF → SPF → Pack → Downstream | draft |
| DP.ONT.001 | Онтология платформы | ONT | Единая онтология домена «Цифровая платформа развития интеллекта»: 5 первичных родов сущностей (Созидатель, ИТ-система, Действие, Организация, Артефакт), маршрутизация описаний (type-level → Pack, instance-level → Neon/DS/R2/Legacy), виды сущностей по SPF.SPEC.001, глоссарий, отношения, иерархия типов, кросс-Pack связи, реестр различений, аббревиатуры. | active |
| DP.ORG.001 | Организация (род сущности) | ORG | Организация — коллективный субъект платформы: юр.лицо или сообщество со службами, сотрудниками, процессами. Первичный род наряду с Созидателем, ИТ-системой, Действием, Артефактом. Подтипы: МИМ, Aisystant, ШСМ. Целевая физ.реализация — схема platform-core #1 Neon (organizations/departments/employments) через ArchGate при первом FK. | draft |
| DP.ROADMAP.001 | План миграции Neon 9 → 12 БД | ROADMAP | Фазовый план перехода Neon с 9 БД (v1 14 апр) на 12 БД (согласно DP.ARCH.004 §1 v2.3). P0 подготовка, P1 низкорисковые переименования, P2 роспуск activity-hub, P2b dt-collect миграция на event-gateway, P3 расщепление platform, P4 knowledge split + aist-bot, P5 новые БД (#10/#11/#12), P6 decommissioning, P7 verification ongoing. Gating-критерии, rollback playbook, координация с child-WP, матрица рисков. | draft |
| DP.ROADMAP.002 | Neon MVP-greenfield (infra-first, старт 24 апр) | ROADMAP | Параллельный к основному Roadmap план: MVP-greenfield на 12 целевых БД (DP.ARCH.004 v2.4), infra-first. Cut-over W18 executed 26-27 апр. Ф9.1-Ф9.4 internal gates PASS, Ф9.5 core-team prep активен, Ф9.6-Ф9.8 запланированы. Нумерация Ф9.X выровнена с context-файлом WP-253. | in_progress |
| DP.ROLE.001 | ИИ-системы | ROLE | Реестр и классификация ИИ-систем платформы: роли (Стратег, Экстрактор, Проводник и др.) и исполнители (Claude, бот, скрипты) | active |
| DP.ROLE.012 | Стратег (Strategist) | ROLE | Роль Стратег (R1) — стратегирование (WHAT/WHY): discovery неудовлетворённостей, диагностика состояния, приоритеты месяца. Операционное планирование (неделя/день) передано Плановику (DP.ROLE.066), РП378. | draft |
| DP.ROLE.012.SC.01 | 01 Strategy Session | ROLE | Еженедельная сессия стратегирования (strategy_day 7:00): ревью НЭП, анализ прошлой недели, сдвиг фокуса месяца, формирование плана на неделю | active |
| DP.ROLE.012.SC.02 | — План дня | ROLE | Ежедневное планирование (7:00): апдейт вчера по коммитам, контекст недели и план дня с рекомендацией старта | draft |
| DP.ROLE.012.SC.03 | 03 Week Review | ROLE | Итоговое ревью недели (вс 22:00): агрегация дневных планов, анализ коммитов, расчёт статистики и публикация в клуб | draft |
| DP.ROLE.012.SC.04 | 04 Month Report | ROLE | Итоговый отчёт за месяц: агрегация недельных данных, проверка выполнения приоритетов, анализ трендов и достижений | draft |
| DP.ROLE.012.SC.05 | 01 Evening Review | ROLE | Вечерний итог дня по запросу: сопоставление коммитов со статусами РП, выявление незапланированного, carry-over на завтра | draft |
| DP.ROLE.012.SC.06 | 02 Check Plan | ROLE | Сверка задачи с планом по запросу: классификация на in-plan / aligned / unplanned / urgent с рекомендациями действия | draft |
| DP.ROLE.012.SC.07 | 03 Update Priorities | ROLE | Изменение приоритетов на уровне дня/недели/месяца: определение типа изменения, каскадные эффекты, diff и коммит | draft |
| DP.ROLE.012.SC.08 | 04 Add Workproduct | ROLE | Добавление нового РП в план: сбор атрибутов, проверка бюджета, определение уровня размещения и коммит в план | draft |
| DP.ROLE.012.SCENARIOS | 00 Scenarios Index | ROLE | Индекс и навигация по 8 сценариям Стратега: 4 по расписанию и 4 по запросу, с временной сеткой и потоком данных | draft |
| DP.ROLE.013 | Проводник (Conductor) | ROLE | FSM-апдейтер доступного функционала. Получает сигнал о достижении пилотом условий открытия (подписка + N дней + ступень) и обновляет состояние доступных команд/кнопок/скиллов. Не принимает решение о готовности — решение принимает Контролёр развития (DP.ROLE.046). Не оценивает — оценивает Аттестатор (DP.ROLE.041). Не является стратегической доменной ролью — это инфраструктурный агент (infrastructure-agent) для feature unlocking T1→T4. | draft |
| DP.ROLE.022 | Оркестратор (Orchestrator) | ROLE | Координатор цикла персонального развития: решает ЧТО и КОГДА запускать, делегирует исполнение специализированным Контролёрам и операционным ролям. На уровне суперсистемы координирует Контролёров (DP.ROLE.046 и его специализации); ниже — взаимодействует с Портным, Навигатором, Диагностом, Аттестатором, Проводником. | draft |
| DP.ROLE.023 | Верификатор (R23) | ROLE | Sub-agent роль проверки артефактов по эталону (Pack/SPF/чек-лист) с context isolation. Возвращает PASS/FAIL с обоснованием. Не правит проверяемое, не выносит решение о допуске. | active |
| DP.ROLE.024 | Аудитор | ROLE | Роль контроля полноты покрытия Pack'ов и DS-артефактов: сканирует целевое множество по индексу, выявляет gap'ы методами VR.M.002 (кросс-контекст) и VR.M.004 (полнота), формирует отчёт coverage % для заказчика. Инвариант: методологическая независимость (context isolation + read-only + формальный метод) — не организационная дистанция. Семейство: Контрольные (VR), маппинг VR.R.002. | draft |
| DP.ROLE.031 | Терминолог | ROLE | Роль Терминолог отвечает за качество терминологии Pack: выбор переводов, онтологическое сопоставление с FPF, разрешение конфликтов имён. | draft |
| DP.ROLE.032 | Event Ingester | ROLE | Роль единого приёмника доменных событий обучения от всех источников — гарантирует идемпотентность, валидацию и защиту от PII на входе в learning.domain_event | draft |
| DP.ROLE.033 | Редактор контента | ROLE | Роль, читающая черновики автора и выдающая рекомендацию топ-3 в Day Open на основе актуальности и готовности. | draft |
| DP.ROLE.034 | Rewards Projector | ROLE | Роль проектора баллов: читает learning.domain_event, применяет reference.reward_rules через compute_effective_amount, пишет в rewards.point_balances идемпотентно через cursor | draft |
| DP.ROLE.035 | Platform Observer | ROLE | Роль наблюдателя за здоровьем платформы — оркеструет Better Stack (external observability owner), AIST Bot (TG-алерты команде + автопостинг канал), Neon `health.internal_metrics` (узкая projection для JOIN с business). | draft |
| DP.ROLE.036 | Коннектор клуба | ROLE | Носитель потока данных systemsworld.club (Discourse) → Neon. Read-only ingest активности участников через webhook + polling backfill, с lazy-резолвом discourse_user_id ↔ ory_identity_id после ORY-SSO. | draft |
| DP.ROLE.037 | Регистратор РП | ROLE | Координатор целостности: гарантирует, что статус любого РП одинаков во всех 5 хранилищах IWE. Не исполняет работу по РП — исполняет работу ПО МЕТАДАННЫМ РП. | active |
| DP.ROLE.038 | MCP Tool Consumer | ROLE | Посредник между LLM-клиентом (бот) и платформенными MCP-серверами: загружает актуальный список tool через discovery (tools/list), кэширует с TTL, фильтрует по tier, передаёт в Claude API без hardcoded списков в коде. | draft |
| DP.ROLE.039 | Peer Agent (равноправный peer-агент в multi-agent сессии) | ROLE | Peer-агент в multi-agent IWE сессии: работает в одном из двух режимов — (A) workspace-координация через Local Gateway lock + peer-status, (B) conversational-сессия через журнал реплик с позициями писатель/напарник. Конкретные инстансы: Claude Code, Kimikode, Aider и т.п. | draft |
| DP.ROLE.040 | OAuth Orchestrator (единая точка OAuth-flows для всех каналов IWE) | ROLE | Сервис-роль: принимает OAuth setup/callback запросы от web/vscode/bot каналов, разрешает identity (Ory > telegram > github), управляет state-token lifecycle, координирует token exchange с провайдерами (GitHub App, Linear, Twin, Google Cal, WakaTime, Ory), хранит токены encrypted-at-rest в Neon. Не зависит от bot process. | draft |
| DP.ROLE.041 | Аттестатор | ROLE | Роль автоматического вычислителя ступени Ученика: читает события из Activity Hub, считает 7 bh-характеристик (bh.sys/inv/met/awr/agn/scl/stb) по двум осям (Мастерство × Мировоззрение), сравнивает с нормативной матрицей и записывает bh-сигнал в learning.stage_transitions. Итоговую ступень фиксирует двойной gate: bh-сигнал Аттестатора + cp-подтверждение Диагноста (MIM.R.009). Болид-онтология: Аттестатор измеряет Пилота, не всего Созидателя. | draft |
| DP.ROLE.042 | Диагност (R28) | ROLE | Роль диалоговой и фоновой диагностики ученика: проводит диалог ≤5 вопросов (три фазы), вычисляет cp-профиль (ступень + bottleneck + recommended_stream + skip_to_stage), сохраняет в learning.cp_assessments. Является стартом содержательной оси онбординга (DP.ARCH.002 §2б): диагностика доступна на T1 (free), результат питает get_journey_state (MCP). В фоновом режиме — silent-monitoring сигналов инвалидации и подсказки активным ролям (Навигатор / Портной / Аттестатор). Реализует двойной gate FORM.089 §5.1 с Аттестатором. | draft |
| DP.ROLE.043 | Лаборант | ROLE | Роль симулятора траектории Созидателя: принимает профиль + паттерн поведения, запускает сценарий (Scenario.run() → DataFrame), возвращает траекторию характеристик и ступени во времени — в pilot-mode без технических кодов. | draft |
| DP.ROLE.044 | Notification Dispatcher | ROLE | Транспортный слой исходящих уведомлений платформы: принимает запросы от любых потребителей (пользователь, агент, воркер), ставит в очередь, доставляет в Telegram exactly-once, подтверждает статус. | draft |
| DP.ROLE.045 | Agent Task Dispatcher | ROLE | Координатор очереди агентных задач IWE: читает inbox/agent/tasks/, запускает через подходящий канал (CCR / systemd / local), фиксирует lifecycle и audit-trail. | draft |
| DP.ROLE.046 | Контролёр развития (Development Controller) | ROLE | Ежедневный фоновый сканер: обходит опт-инов, сравнивает фактический профиль с маркером ожидаемого профиля по выбранной оси контроля (по умолчанию — ступени Ученика, FORM.089 §6.3; расширяемо на степени квалификации, стиль, домены). При обнаружении gap'a выдаёт точечное задание адресату по типу разрыва. Не оценивает, не назначает, не учит — только инициирует следующее действие. | draft |
| DP.ROLE.047 | Trace Recorder (Архивариус решений) | ROLE | Записывает рассуждения LLM-агента (гипотезы, выбор, обоснование) в append-only журнал. Single source of truth для retrieval, replay, pattern mining. Не блокирует hot path. | draft |
| DP.ROLE.048 | Replay Engine (Машина повторов) | ROLE | Восстанавливает состояние агента на момент T из trace + событий, создаёт fork-сессию. Детерминированное воспроизведение через checkpoint + reseed. Read-only по исходному trace. | draft |
| DP.ROLE.049 | Path Coordinator (Координатор путей) | ROLE | Разворачивает N кандидатов параллельно на open-loop задачах с разными моделями/seed, координирует селектор, обеспечивает budget guard и сохранение всех путей в trace для последующего анализа. | draft |
| DP.ROLE.050 | Pattern Miner (Старатель паттернов) | ROLE | Кластеризует trace'ы за период по (trace_features, outcome_features) join, формирует кандидатов AR.NNN с примерами, помечает status: pending-review. Никогда не создаёт правила автоматически. | draft |
| DP.ROLE.051 | Points Redeemer (Burn-эмиттер баллов) | ROLE | Роль burn-эмиттера: при чекауте резервирует баллы в rewards.redeemed_events; при webhook'е оплаты подтверждает или откатывает резерв; эмитирует event 'points_redeemed' для projection-worker. Не writer point_balances. | draft |
| DP.ROLE.052 | Когнитивный прокси-аналитик | ROLE | Извлекает косвенные характеристики (cp.wld, cp.agt, bh.awr) из текстового содержания пилота. Результаты используются ТОЛЬКО для рекомендаций (Портной, Диагност) — не для расчёта stage/certificate. Пишет ТОЛЬКО в cognitive-схему через scope guard. | draft |
| DP.ROLE.053 | R29 Декомпозитор | ROLE | — | active |
| DP.ROLE.054 | R30 Аналитик ограничений | ROLE | Носитель методики TOC (Goldratt Five Focusing Steps + Tendon TameFlow Replenishment Cycle + Dettmer Thinking Processes). Идентифицирует систему-конвейер, сканирует функциональные обещания (SC-first), находит ограничение, выбирает TOC-инструмент и выдаёт план работы как карту этапов с зависимостями (без дат/часов). Универсален: применим к учебному конвейеру пилота, конвейеру работ (РП/эпик/проект/репо), когортному конвейеру. | draft |
| DP.ROLE.055 | Агент поддержки IWE | ROLE | Носитель ответа на пилотские обращения через Chatwoot CE: маршрутизирует тикеты по теме (баг → разработчик, баллы → диспетчер, руководство → методист), отвечает в Chatwoot, эскалирует в Linear, поддерживает FAQ и Saved Replies. Граница: НЕ диагностирует архитектурные баги (это R6 Кодировщик), НЕ принимает продуктовые решения по фичреквестам (это Стратег R1 + пилот). | draft |
| DP.ROLE.056 | R32 Мейнтейнер скиллов | ROLE | Владеет каталогом скиллов IWE. Принимает решения о promote L3→L1, отвечает за обратную совместимость при обновлении L1-скиллов, управляет жизненным циклом скилла (active→experimental→deprecated). | draft |
| DP.ROLE.057 | R33 Автор скилла | ROLE | Создаёт новый скилл IWE через конвейер: create-skill.sh → SKILL.md v2 заполнить → validate-skill.sh → smoke-test → propose promote к Мейнтейнеру. Декларирует зависимости, выбирает layer (L1/L3), ссылается на DP.SC. | draft |
| DP.ROLE.058 | R?? Артефактор-Постановщик | ROLE | Агентная роль: превращает сырой запрос пользователя в структурированный РП с routing-тегом (task_type + class), готовый к lookup в executor-catalog.yaml Маршрутизатора. | draft |
| DP.ROLE.059 | R30 Маршрутизатор | ROLE | Единая точка маршрутизации задач IWE: получает запрос с routing-тегом, выбирает исполнителя из executor-catalog.yaml, не классифицирует самостоятельно — исполняет routing-решения WP Gate или Артефактора. | draft |
| DP.ROLE.060 | Презентатор | ROLE | Роль, готовящая и проводящая публичные выступления (доклады, презентации) от имени IWE/MIM. Обеспечивает единый стиль, структурный каркас слайдов и воспроизводимый процесс подготовки. | draft |
| DP.ROLE.061 | External Session Adapter | ROLE | Мост между внешним каналом (Telegram) и локальным исполнителем (Claude Code). Поддерживает multi-turn диалог: каждый ход дописывается в SESSION-thread, Egress запускает Claude Code с полным контекстом. Capability scope: код+git, calendar, WP, IWE-знания. Две sub-responsibility: Ingress (cloud) и Egress (local). | draft |
| DP.ROLE.062 | Создатель паков (R30) | ROLE | Роль LLM-сопровождения автора PACK-X через SPF-цикл наполнения 01-11: вызывает R28 Диагност для определения режима (assembly/hybrid/full SPF), ведёт по фазам, защищает инвариант read-only upstream FPF/SPF. Работает с одним PACK-X за сессию; cross-pack consistency — у R24. | draft |
| DP.ROLE.063 | Менеджер оргразвития (R31) | ROLE | Роль LLM-проводника между запросом субъекта об оргизменении (себя/команды/организации) и методами СИ/СМ/ИЛ программы РР. Шаг 0 — классификация типа системы (MIM.M.030). LLM-stateless по in-memory, file-stateful по контексту субъекта (personal-guide/team-guide). | draft |
| DP.ROLE.064 | Сторож новых задач (issue watcher) | ROLE | Специализированная операционная роль: фоновый скрипт, который ежедневно обходит github-репо IWE, выявляет новые задачи (issues), классифицирует важность и шлёт дайджест пилоту в Telegram. Скрипт ≠ агент (фиксированный flow, без LLM). Один исполнитель = одна роль (специализированный агент по имени роли). | draft |
| DP.ROLE.065 | hermes-proxy-tool | ROLE | — | draft |
| DP.ROLE.066 | Плановик (Planner) | ROLE | Роль операционного планирования (HOW MUCH / WHEN): упаковка приоритетов месяца от Стратега (R1) в рабочие продукты недели с бюджетами, распределение по дням, удержание WIP-лимита. Выделена из R1 Стратега (DP.ROLE.012), который сужается до стратегирования (WHAT/WHY). | draft |
| DP.ROLE.067 | Онбордер | ROLE | — | draft |
| DP.ROLE.068 | Постановщик задачи IWE | ROLE | Член команды T4+. Превращает сырую нужду (баг, идея, замечание) в оформленную задачу для конвейера WP-403 с тегом маршрутизации, классом верификации и критерием приёмки. | draft |
| DP.ROLE.069 | Архитектор конвейера IWE | ROLE | Член команды T4+. Проходит ArchGate и IntegrationGate для задач конвейера WP-403: обещание, сценарии, роли, границы. Сложные решения — согласование с Ведущим (TD1+TA4). | draft |
| DP.ROLE.070 | Верификатор конвейера IWE | ROLE | Член команды T4+ (другой разработчик). Независимая проверка работы Разработчика-исполнителя по эталону перед закрытием РП. Возвращает PASS/FAIL с обоснованием. | draft |
| DP.ROLE.071 | Ведущий разработчик IWE | ROLE | Ведущий разработчик команды IWE (TD1+TA4). Согласовывает merge, принимает архитектурные решения высокого уровня, подписывает рост в команде. | draft |
| DP.ROLE.072 | Разработчик-исполнитель IWE | ROLE | Член команды разработки IWE (T4+ / TD1). Ведёт задачу через 6 станций конвейера WP-403, обеспечивая двойной выход: работающий код/артефакт + зафиксированное знание. | draft |
| DP.ROLE.073 | Хранитель реестра стилей | ROLE | — | draft |
| DP.ROLE.074 | Диспетчер стилей | ROLE | — | draft |
| DP.ROLE.075 | Доставщик (Delivery Policy Layer) | ROLE | Слой политики исходящих: единая воронка всех сообщений пользователю. Применяет глобальный потолок по классу, приоритет, дедуп-по-всем и hard-gate предпочтений, затем передаёт транспорту (DP.ROLE.044) для физической доставки. | draft |
| DP.ROLE.076 | Ревьюер инженерного стиля кода (Code Craft Reviewer) | ROLE | Контрольный агент: по git diff семантически проверяет соответствие крафт-правилам P1-P9 (DP.SC.172), которые механический детектор не ловит. Read-only на код, пишет только в свой канал (лог стиля + отчёт). Advisory-вердикт с severity; не блокирует и не правит. | active |
| DP.ROLE.077 | Учётчик следов (trace-accountant) | ROLE | Единственный authorized writer в learning.domain_event. Принимает следы от сенсорных адаптеров, применяет consent-guard, нормализует, маршрутизирует по route_catalog, управляет trace_stubs и reconciler-отчётом. | draft |
| DP.ROLE.078 | Владелец тира (Tier Authority) | ROLE | Единственный компонент, который вычисляет и пишет traits.tier. Операционная роль: меняет состояние персоны. Носитель — user-profile-service. | draft |
| DP.ROLE.079 | Bot Agent Session Orchestrator | ROLE | Оркестратор live-агентной сессии IWE через Telegram: выбирает исполнителя (Claude/Kimi/Hermes) через factory, ведёт lifecycle сессии (start→run→pause→resume→close), принуждает audit + domain-scope, возвращает артефакты в Telegram. Не путать с Диспетчером очереди задач (DP.ROLE.045). | active |
| DP.ROLE.080 | Владелец выдачи согласия на анализ данных (Consent Grant Authority) | ROLE | Единственный компонент, который пишет scope=data_analysis в learning.consent_grant и эмитит consent_granted в public.domain_event. Не отвечает за revoke, не отвечает за text_analysis/typing_tracking (владелец — бот). | draft |
| DP.ROLE.081 | Ретранслятор канона | ROLE | Механически переносит содержимое канона в целевую организацию при каждом изменении — по активному обещанию (перевод или зеркалирование), не решает, что и когда публиковать. | draft |
| DP.RUNBOOK.001 | Runbook: Aist Bot Errors | RUNBOOK | Операционный runbook. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.SC.001 | Планирование дня | SC | Пользователь получает ясный план работы на день к началу рабочего дня | draft |
| DP.SC.002 | Планирование и ревью недели | SC | Пользователь получает план недели на основе стратегии и итоги прошедшей недели | draft |
| DP.SC.003 | Обучение и развитие | SC | Пользователь получает персонализированное развитие: вопросы, проверку ДЗ, ленту знаний, марафоны | draft |
| DP.SC.004 | Фиксация и экстракция знаний | SC | Знания фиксируются в момент обнаружения и превращаются в формализованные Pack-сущности | draft |
| DP.SC.005 | Публикация контента | SC | Автор пишет лонгрид для клуба (source-of-truth), согласовывает, адаптирует под каналы, публикует автоматически или вручную | draft |
| DP.SC.006 | Автоматическое обслуживание | SC | Платформа автоматически синхронизирует данные, проверяет целостность и поддерживает инфраструктуру без участия пользователя | draft |
| DP.SC.007 | Триаж и техдолг | SC | Негативная обратная связь автоматически классифицируется, а техдолг приоритизируется в сессиях триажа | draft |
| DP.SC.008 | Самовосстановление | SC | Платформа автоматически обнаруживает и устраняет сбои — от зависших пользователей до критических ошибок | draft |
| DP.SC.009 | Аналитика и метрики | SC | Пользователь получает агрегированные метрики по качеству ответов, активности и затратам времени | draft |
| DP.SC.010 | Рабочий ритм (ОРЗ) | SC | Рабочий день и каждая сессия структурированы по циклу Открытие → Работа → Закрытие — ничего не забыто, всё зафиксировано | draft |
| DP.SC.011 | Стратегирование и Планирование | SC | Зонтичное обещание сквозного цикла: неудовлетворённости → приоритеты (стратегирование) → утверждённый план недели (планирование). Реализуется двумя ролями (Стратег + Плановик) через DP.SC.030 + DP.SC.051. | draft |
| DP.SC.012 | Онбординг | SC | Новый пользователь настраивает IWE и понимает что делать — от первого запуска до первого рабочего дня | draft |
| DP.SC.013 | Рабочая сессия с Claude Code | SC | Пользователь выполняет задачу с ИИ-ассистентом — от WP Gate до Close с фиксацией знаний | draft |
| DP.SC.014 | Формализация знаний (Pack) | SC | Доменное знание формализуется в Pack-структуру — методы, различения, failure modes, SOTA, рабочие продукты | draft |
| DP.SC.015 | Развитие системы (DS) | SC | Новая функциональность спроектирована и реализована — от UC Gate до работающего сервиса с PROCESSES.md | draft |
| DP.SC.016 | Коллективное управление рабочими продуктами | SC | Команда с индивидуальными IWE работает над общими и назначенными РП — каждый видит свои задания, общую картину и прогресс коллег | draft |
| DP.SC.017 | Адаптивное задание на день | SC | Платформа формирует персональный план дня для участника потока — с учётом тира, прогресса вчера, ступени квалификации и целей программы — и трекает выполнение | draft |
| DP.SC.018 | Переход T3 → T4 (присоединение к git) | SC | Участник дорос до самостоятельного управления своим IWE — платформа помогает перейти от получателя заданий к их автору | draft |
| DP.SC.019 | Автономная работа IWE (Cloud Runtime) | SC | IWE работает 24/7 в облаке: ночная автоматика, мультиустройственный доступ, управление через Telegram | draft |
| DP.SC.020 | Персональная программа развития | SC | Платформа ведёт пользователя через программу «Личное развитие» — от ступени Случайный до Проактивный — через цикл диагностика → сборка занятия → доставка → оценка → фиксация прогресса. Четыре агентные роли (Диагност, Оркестратор, Портной, Навигатор) работают совместно, адаптируя содержание, темп и глубину под конкретного пользователя.
 | draft |
| DP.SC.021 | Mcp Knowledge Access | SC | — | draft |
| DP.SC.022 | Personal Knowledge Indexing | SC | — | draft |
| DP.SC.023 | Mcp Extensibility | SC | — | draft |
| DP.SC.024 | Iwe Maintenance | SC | — | draft |
| DP.SC.025 | Capture Bus | SC | — | draft |
| DP.SC.026 | Мониторинг поведения агента | SC | — | draft |
| DP.SC.027 | Repo Touch Gate | SC | — | draft |
| DP.SC.028 | Семиотическое качество Pack | SC | Pack-автор получает верифицированные сущности с корректной Kinds-структурой, защитой от лексической дедупликации и читаемым смыслом для агентов и людей | draft |
| DP.SC.029 | Терминологический процесс IWE | SC | Автор понятия получает верифицированный перевод name_ru/name_en и сопоставление с FPF-корнем при вводе нового понятия в Pack | draft |
| DP.SC.030 | Разговор-распаковка неудовлетворённостей (discovery-стратегирование) | SC | R1 Стратег ведёт пилота сократическим диалогом от сырой рефлексии и заметок к структурированным неудовлетворённостям, состоянию и приоритетам месяца (WHAT/WHY), не подсказывая формулировки за него. | active |
| DP.SC.031 | Personal Read Api | SC | — | draft |
| DP.SC.032 | Personal Data View Audit | SC | — | draft |
| DP.SC.033 | Целостность жизненного цикла РП | SC | Стратег получает гарантию: статус любого РП одинаков во всех 5 хранилищах IWE в течение ≤5 минут после любого изменения. Нарушение = drift, обнаруживается автоматически. | active |
| DP.SC.034 | Local MCP Gateway для multi-agent VS Code | SC | Peer-агент (Claude Code, Kimikode и т.п.) в одной VS Code сессии получает гарантию: tool-вызовы маршрутизируются через единую точку, конфликт записи в один файл предотвращается pessimistic-lock'ом, новый агент подключается без правки кода других агентов. | draft |
| DP.SC.035 | Peer-agent choreography (turn-based координация) | SC | Пилот получает гарантию: два+ peer-агента (Claude Code + Kimikode и др.) в одной VS Code сессии работают параллельно над разными файлами без дублирования и race-condition. Координация — turn-based через lock API Local Gateway + git sequential commits для sync. | draft |
| DP.SC.036 | Knowledge Routing Gate — маршрутизация артефактов в IWE | SC | Агент получает канонический путь размещения для любого нового артефакта до выполнения Write, используя каскад vocab → DP.KR.001 → repo CLAUDE.md | draft |
| DP.SC.037 | Agent Trace | SC | — | draft |
| DP.SC.038 | Agent Replay | SC | — | draft |
| DP.SC.039 | Multipath | SC | — | active |
| DP.SC.040 | Pattern Miner | SC | — | draft |
| DP.SC.041 | Индикатор мультипликатора IWE в характеристике мастерства | SC | Потребители (Аттестатор, Навигатор, Metabase) получают в digital_twins.data['3_derived']['3_2_mastery'] четыре числа: multiplier_auto, multiplier_manual, multiplier_drift, multiplier_7d_avg. Расхождение — сигнал, не ошибка. | active |
| DP.SC.042 | Извлечение знаний в Pack | SC | Знания из сессий, обратной связи и документов преобразуются в Pack-сущности (правила, роли, методы, различения) и интегрируются в платформу | active |
| DP.SC.043 | Обновление экзокортекса | SC | Пользователь получает обновления платформенных файлов шаблона — новые протоколы, скиллы, скрипты, исправления | draft |
| DP.SC.044 | Event Ingest (единый приёмник доменных событий) | SC | Единая точка приёма доменных событий обучения от всех источников с идемпотентностью, валидацией и PII-фильтрацией | draft |
| DP.SC.045 | Анализ ограничения системы (TOC) | SC | Потребитель (пилот / Стратег / Декомпозитор / Навигатор) получает на выходе пятифазного ВДВ-каскада три артефакта: System Card (классификация системы-конвейера), Constraint Brief (описание ограничения с trichotomy + class), Stage Dependency Map (план работы как dependency graph без дат и часов). SC-first: первой проверяется работоспособность функциональных обещаний, не структура pending-РП. | draft |
| DP.SC.046 | Runtime-цикл IWE (open → work → close) | SC | IWE гарантирует, что любая рабочая сессия проходит через три обязательные фазы — open, work, close — независимо от хоста (Claude Code, Hermes runtime, бот). Контракт хост-агностичный: протоколы определяются слоем 4, не слоем 3. | draft |
| DP.SC.047 | Презентация к публичному событию | SC | Подготовка и проведение публичного выступления (доклада, презентации) с единым стилем IWE/MIM. Вход — тема и событие, выход — Marp-слайды + PDF + отчёт + post-deck пакет. | draft |
| DP.SC.048 | Создатель паков | SC | Автор PACK-X получает: LLM-сопровождение через весь SPF-цикл наполнения собственного Pack 01-11, с режимом по cp-профилю (assembly/hybrid/full SPF) и защитой инварианта read-only upstream FPF/SPF. | draft |
| DP.SC.049 | Менеджер оргразвития | SC | Пилот (как субъект изменения себя/команды/организации) получает: классификацию типа системы (личность/команда/организация), проверку applicability, выбор метода (СИ/СМ/ИЛ) и конкретный первый шаг ≤30 мин из одного из канонических руководств программы РР. | draft |
| DP.SC.050 | Единый разговорный стиль агентов | SC | Каждый агент (Claude, Kimi, Hermes) получает единую базу разговорного стиля и исполняет её при общении с людьми | active |
| DP.SC.051 | Совместный недельный ритуал Стратега и Плановика | SC | Недельное планирование — совместный ритуал R1 Стратега (DP.ROLE.012) и Плановика (DP.ROLE.066): Плановик ведёт упаковку приоритетов в неделю; Стратег подключается, только если приоритеты устарели или состояние пилота изменилось. | active |
| DP.SC.052 | vdv-skill | SC | Генерирует описание стадийного процесса по методу ВДВ или проверяет готовое описание по 6 принципам сцепки входов-выходов | active |
| DP.SC.053 | Локальная сборка персонального руководства (приватный IWE-контур) | SC | Для пользователя со своим IWE и строгим приватным контуром персональное руководство собирается ЛОКАЛЬНО (в среде пользователя), а не на платформе: сырой личный контент (РП, рефлексии, captures, current_request) не покидает контур пользователя. Платформа отдаёт только производные (derived) данные и универсальное знание через MCP-шлюз под явным consent. Один метод DP.M.241, исполняемый в IWE-локусе; managed-обещание платформы — DP.SC.164. | draft |
| DP.SC.101 | Вход и онбординг на платформе | SC | Новый участник регистрируется, создаёт ЦД и получает персональный стартовый маршрут — от любопытства к первому действию | draft |
| DP.SC.102 | Непрерывное обучение | SC | Участник изучает руководства, выполняет задания в рабочей тетради, получает обратную связь от наставника или ИИ | draft |
| DP.SC.103 | Работа над целевыми системами | SC | Участник применяет методологию FPF к реальным проектам — от учёбы к созиданию | draft |
| DP.SC.104 | Адаптивная персонализация через Персону, Память и Проекцию | SC | Платформа адаптируется под человека через три слоя пользовательской модели (Персона декларативная, Память наблюдаемая, Проекция runtime) и три механизма персонализации (персонализация/индивидуализация/адаптивность) | draft |
| DP.SC.105 | Экономика вклада — баллы и репутация | SC | Участники получают баллы за подтверждённые действия, бонусы конвертируются в доступ к сервисам | draft |
| DP.SC.106 | Сообщество и культурная диффузия | SC | Участники обсуждают, менторят, проверяют работы друг друга и несут культуру вовне | draft |
| DP.SC.107 | Мультиповерхностный доступ | SC | Одна платформа, много интерфейсов — Web App, Telegram-бот, Claude Code CLI, Discord | draft |
| DP.SC.108 | Формирование команд | SC | Участники формируют гибридные команды (люди + ИИ-агенты) для работы над целевыми системами | draft |
| DP.SC.109 | Масштабирование — Global Core + Local Edge | SC | Платформа масштабируется через единое ядро методологии и локальные адаптации (язык, кейсы, compliance) | draft |
| DP.SC.110 | Управление потоками и наставничество | SC | Администратор создаёт потоки, назначает наставников; наставник проверяет ДЗ и ведёт группу; сертификация автоматическая | draft |
| DP.SC.111 | Назначение на позицию | SC | Администратор назначает позицию (бандл Role+Tier+Scope) одним действием — система раскладывает в три оси доступов | draft |
| DP.SC.112 | Подписка и оплата | SC | От бесплатного старта к устойчивой подписке — тиры T1-T4, YooKassa/Stripe/TG Stars, баллы, revenue sharing | draft |
| DP.SC.113 | Авторство и Revenue Sharing | SC | Автор создаёт руководство, публикует через рецензирование и получает долю дохода (50%) | draft |
| DP.SC.114 | CRM и работа с участниками | SC | Воронка, удержание, отток, группы — управление на основе данных с проактивной работой с at-risk участниками. Directus UI + Telegram CRM-команды + Metabase дашборды | draft |
| DP.SC.115 | Маркетинг и привлечение | SC | Привлечение участников через открытые руководства, рефералы, промо-коды и конверсионные триггеры в боте | draft |
| DP.SC.116 | Уведомления и nudges | SC | Правильное сообщение в правильный момент — ЦД-инсайты, дедлайны, streaks, milestones, конверсия | draft |
| DP.SC.117 | Асинхронная проверка и обсуждение ДЗ | SC | Ответы на ДЗ сохраняются в Память.Observed, проверяются пакетно, результаты персистентны и доступны для обсуждения | draft |
| DP.SC.118 | Ассистент упоминаний в каналах | SC | Бот отслеживает упоминания пользователя в TG-каналах, генерирует черновик ответа через IWE и присылает в личку | draft |
| DP.SC.119 | Рабочее пространство из браузера | SC | Пользователь создаёт и управляет IWE-пространствами из браузера — Pack, DS-репо, заметки — без git, без терминала, без VS Code | draft |
| DP.SC.120 | Приёмник платежей (Payment Receiver) | SC | Webhook-приёмник: провайдеры (YooKassa, Stripe, Paybox) → verify → normalize → idempotent write → finance_payments (Neon) | draft |
| DP.SC.122 | Rewards Projection (точная проекция баллов по доменным событиям) | SC | Точная идемпотентная проекция из learning.domain_event в rewards.point_balances по reference.reward_rules через LISTEN/NOTIFY | draft |
| DP.SC.123 | Platform Observability (internal — наблюдаемость инфраструктуры для команды) | SC | Минимально достаточный набор сигналов о здоровье 12 БД и ~10 сервисов для команды: реактивные ответы, проактивные алерты, retro-queries. SaaS-first (Better Stack owner external observability) + узкая projection в Neon для JOIN с business-данными. | draft |
| DP.SC.124 | Lifework Pack Coaching | SC | Созидатель получает поэтапную помощь Портного в составлении документа очередного уровня охвата пакета Lifework при условии, что документ предыдущего уровня работает ≥6 месяцев | draft |
| DP.SC.125 | Гостевой пропуск (реферальная виральность БР) | SC | Подписчик БР приглашает друга → друг получает 14 дней бесплатного БР → при оплате друга и удержании 30 дней приглашающий получает 1 месяц БР | draft |
| DP.SC.126 | Подписка БР как массовый продукт | SC | Участник получает персональную траекторию роста интеллекта на всю жизнь — не курс по навыкам, а среда с памятью о нём, которая адаптируется через методологию, платформу и адаптивную персонализацию | draft |
| DP.SC.127 | Редактор контента | SC | Автор получает топ-3 черновика для работы и сигналы о готовых постах в Day Open | draft |
| DP.SC.128 | Ingest активности клуба (Discourse) | SC | Платформа получает события активности участников клуба systemsworld.club для расчёта баллов и аналитики | draft |
| DP.SC.129 | Generic MCP Tool Discovery (бот → платформенные MCP) | SC | Бот получает актуальный список tool из платформенных MCP при старте и периодически, без hardcoded списков в коде | draft |
| DP.SC.130 | OAuth Gateway (единая точка OAuth для всех каналов IWE) | SC | Web/VS Code/Bot пилот получает доступ к внешним OAuth-провайдерам (GitHub App, Linear, Twin, Google Calendar, WakaTime, Ory) через единый endpoint с dual identity (telegram_user_id ИЛИ ory-session) | draft |
| DP.SC.131 | Автопроцесс резервного копирования данных IWE | SC | — | — |
| DP.SC.132 | Диагностика ученика (Диагност) | SC | Пилот (Ученик), Аттестатор, Портной или Навигатор получает cp-профиль (ступень, bottleneck, рекомендуемый поток) через диалог ≤5 вопросов или кэш-ответ, из любого из трёх интерфейсов (TG / браузер / VS Code) или в фоновом режиме | draft |
| DP.SC.133 | Симулятор траектории Созидателя | SC | Пилот получает траекторию своих характеристик и ступени во времени при заданном паттерне поведения — в понятном тексте без технических кодов | draft |
| DP.SC.134 | Notification Dispatcher | SC | Любой потребитель (пользователь, агент, воркер) получает доставку сообщения в Telegram — немедленно или по расписанию — с подтверждением и гарантией exactly-once | draft |
| DP.SC.135 | Agent Inbox — конвейер агентных задач IWE | SC | Создатель IWE ставит задачу агенту в единое место и получает результат в декларированной точке не позднее чем через 1 час после due | draft |
| DP.SC.136 | Rewards Transparency (понимание пилотом источника своих баллов) | SC | Пилот видит не просто число «у тебя X баллов», а понятную причинно-следственную цепочку: за что начислено, сколько по каждому правилу, какие правила игры действуют сейчас. | draft |
| DP.SC.137 | Rewards Analytics (аналитика начислений и прогноз скидок для команды) | SC | Команда (R5 CRM/админ платформы) видит динамику начислений баллов, активные балансы по сегментам пилотов и ожидаемую нагрузку на платформу от конвертации баллов в скидки — без SQL, через Метабазу. | draft |
| DP.SC.138 | Rewards Rules Simulation Lab (симулятор «что если» для калибровки правил) | SC | R2 Архитектор правил может за 5 минут получить ответ «что бы получили пилоты при таком наборе правил» — без деплоя, на исторических данных. Калибровка перед выкаткой. | draft |
| DP.SC.139 | Контролёр развития (Daily Marker Scan) | SC | Опт-инный пилот ежедневно получает корректирующий нудж (TG, render-задача Портному, или сигнал Навигатору/Проводнику/Диагносту) по выбранному профилю контроля (по умолчанию — ступени Ученика и маркеры cp.iwe × cp.cre, FORM.089 §6.3). Профиль контроля расширяемо: степени квалификации, стиль, домены. | draft |
| DP.SC.140 | Club Action Catalog | SC | — | active |
| DP.SC.141 | Зачёт баллов в оплату | SC | Канал «Баллы» в Billing Module: участник применяет накопленные баллы как скидку к оплате сервиса (резерв-перед-оплатой, двухфазный коммит) | draft |
| DP.SC.142 | Текстовый анализ косвенных характеристик (cp.wld / cp.agt / bh.awr) | SC | Портной и Диагност получают актуальные прокси cp.wld, cp.agt, bh.awr из текстового содержания пилота — ТОЛЬКО для рекомендаций. В расчёт stage/certificate не входят. | draft |
| DP.SC.143 | LMS Subscription Webhook (Bridge-2 контракт с LMS Aisystant) | SC | Контракт endpoint'а на стороне LMS Aisystant для приёма подписок от нашего payment-receiver. Артефакт для передачи Диме. | draft-not-delivered |
| DP.SC.144 | User-Facing Platform Health (информирование пользователей о здоровье платформы) | SC | Public status page (status.aisystant.ru) с composite uptime «по девяткам» (формат 99.847%), real-time информирование пользователей об инцидентах через email/RSS subscriptions + TG-канал @aisystant_status. Реализуется через Better Stack SaaS. | draft |
| DP.SC.145 | Llm Router | SC | — | active |
| DP.SC.146 | Secret Drift Detector | SC | — | active |
| DP.SC.147 | Агрегирующий пайплайн cognitive brief | SC | Навигатор (MIM.R.007) перед ответом читает агрегированный brief из выходов Оркестратора, Портного, activity_log и Cognitive Proxy. Без text_analysis consent — только детерминированные поля. | draft |
| DP.SC.148 | Pack Graph Freshness | SC | Pack-граф (concept_graph_nodes + edges) обновляется автоматически при push в Pack-репо и проверяется daily heartbeat + drift detector | draft |
| DP.SC.149 | Ретроспективный майнинг корпуса в PACK-rhetoric | SC | Автор или агент получает пакет карточек иллюстраций из произвольного корпуса (клуб, руководства, книги) в формате RHE.FORM.001 при указании источника и фильтра тропа | active |
| DP.SC.150 | Поддержка пользователей IWE через @aist_me_bot + Chatwoot | SC | Пилот через команду /support в @aist_me_bot открывает тикет в Chatwoot CE; служба поддержки получает структурированный контекст (telegram_id, ory_uuid, последние события), отвечает в Chatwoot — ответ доставляется в TG-чат пилота с префиксом 🛟; SLA ≤24ч на первый ответ | draft |
| DP.SC.151 | Контролёр развития (профиль Onboarding Tick) | SC | Опт-инный пилот R2 получает поведенческий нудж (TG или render-задача Портному) по очереди из 11 онбординговых сообщений (WP-343) + независимые upgrade-маркеры T1→T4 (WP-349: B-low/B-high/C/E). Сообщение приходит не по расписанию, а по реальному поведению пилота. Не более 1 нуджа в сутки. Следующее сообщение доставляется в течение 8h после срабатывания триггера. | active |
| DP.SC.152 | Анализ ограничения ИТ-платформы (platform-bottleneck) | SC | Стратег или CTO получает Constraint Brief с конкретной C2-подсистемой из MAP.002, где максимальное число failing SC, + Stage Dependency Map для устранения. Отличие от SC.045: target жёстко ограничен C2 ИТ-платформой, SC-scan идёт по MAP.002 (12 подсистем, SC.001-SC.151), не по произвольному конвейеру. | draft |
| DP.SC.153 | Скилл-система IWE | SC | Разработчик IWE получает: каталог всех скиллов с метаданными и графом зависимостей; конвейер создания (create-skill.sh → validate → promote); безопасное обновление через versioning без перезаписи L3-кастомизаций. | draft |
| DP.SC.154 | Мульти-агентная диалоговая сессия | SC | Пилот ставит задачу команде из 2+ peer-агентов разных вендоров; они многотурово обсуждают её, согласуют единый отчёт; любой может эскалировать к пилоту при принципиальном несогласии. | draft |
| DP.SC.155 | Маршрут оснащения (Setup Journey) | SC | Пилот R2 на T1 открывает /setup и получает актуальный дашборд прогресса по пути T1→T4: текущий тир, ступень мастерства, что подключено, следующий шаг с CTA. Дашборд читает свежие данные (tier_detector + cp_assessments + onboarding_state) через asyncio.gather. Guided flow проводит шаг за шагом без повторных нажатий (double-tap protection). Последнее CTA-действие пишет last_nudge_at — предотвращает дубль от onboarding_controller в течение 24h. | draft |
| DP.SC.156 | Обнаружение возможностей уровня (Что ещё?) | SC | Пользователь T1-T4 получает список доступных команд своего уровня одним нажатием из tier-экрана | draft |
| DP.SC.157 | Оптимизированный вход в марафон | SC | T1-пользователь получает первый урок марафона за 4 действия от /start, без ручного ввода команд | draft |
| DP.SC.158 | Канон tier-сообщений бота | SC | Пользователь T1-T4 видит единообразное сообщение об уровне по шаблону с номером тира и описанием доступного | draft |
| DP.SC.159 | Маршрутизатор задач IWE | SC | Пилот или агент получает: единственного исполнителя для любой входящей задачи — детерминированного (скрипт) или рассуждающего (LLM/скилл) — в соответствии с routing-тегом, проставленным WP Gate или Артефактором. | draft |
| DP.SC.160 | Артефактор-Постановщик задач IWE | SC | Пилот или Маршрутизатор получает: из сырого запроса — структурированный РП с routing-тегом (task_type, class, artifact, budget_estimate), готовый к lookup в executor-catalog. | draft |
| DP.SC.161 | Session Memory Injector | SC | Pre-flight сервис: читает iwe_memory.db, выбирает 0–3 релевантных напоминания и инжектирует их в системный промпт исполнителя. При сбое — graceful degradation (пустой контекст), ошибка логируется. | draft |
| DP.SC.162 | External Session Request | SC | Пилот ведёт полноценную multi-turn рабочую сессию через Telegram — эквивалент окна VS Code, но асинхронно. Поддерживаются: диалог вопрос→ответ→вопрос, работа по РП, операции с календарём, создание РП, поиск по IWE. Все действия трекаются. | draft |
| DP.SC.163 | Серверные агенты через Gateway (MVP) | SC | Пользователь через Gateway получает результат работы агента (Стратег, Экстрактор) в виде коммита в свой GitHub-репозиторий — без локального CLI, с тем же артефактом, что и через VS Code | draft |
| DP.SC.164 | Доставка персонального руководства пилоту | SC | Ежедневный daily и еженедельный weekly файл персонального руководства, отражающий контекст пилота (активные РП, captures, посты, рефлексии, cp-профиль), доставляется в его репо `personal-guide/<пилот>/` по расписанию; не зависит от ритуалов ОРЗ. | draft |
| DP.SC.165 | Scope-control для bridge write-tools | SC | Bridge write-tools (`personal_write`, `personal_propose_capture`) проходят server-side scope check в gateway-mcp; bridge cache TTL=60s даёт быстрый deny без round-trip | draft |
| DP.SC.166 | Сторож новых задач — ежедневный дайджест в Telegram | SC | Раз в сутки (до 09:00) обойти все github-репо в ~/IWE/*, найти задачи, созданные за последние 2 дня и ещё не показанные пилоту, классифицировать важность и отправить дайджест в Telegram. Критичные (потеря данных / безопасность / регрессия) — отдельной пометкой. Дедуп через state-файл, идемпотентно. | draft |
| DP.SC.167 | hermes-chat | SC | — | draft |
| DP.SC.168 | Онбординг платформы — зонтичное обещание | SC | — | draft |
| DP.SC.169 | conductor-lite | SC | — | deprecated |
| DP.SC.170 | onboarder | SC | — | draft |
| DP.SC.171 | conveyor-development | SC | — | draft |
| DP.SC.172 | База инженерного стиля кода | SC | Агент-разработчик выдаёт код craft-уровня (без перечисленных запахов) при написании кода в репозиториях IWE | active |
| DP.SC.174 | Диспетчер контекста стилей | SC | Диспетчер вычисляет полный композитный ключ из сырого контекста хода (детектор канала + роль читателя), запрашивает фрагмент у реестра и инъектирует его в промпт до первого токена | draft |
| DP.SC.175 | Выбор стиля пользователем | SC | Пользователь настраивает стиль по осям (модель Grammarly), пресетам или из текста-примера; выбор пишется как user_override_hash в каскад платформа→канал→пользователь и применяется со следующего хода | draft |
| DP.SC.176 | Табло показателей пользователя | SC | Авторитетная панель показателей пользователя за прошедший день (5 тайлов РП414) считается вне критического пути (после закрытия суток), публикуется как зафиксированный факт дня со статусом свежести; потребители (Day Open, личная страница, фоновые роли) только читают, не пересчитывают | draft |
| DP.SC.177 | Доставщик (слой политики доставки) | SC | Единая точка, через которую физически уходят ВСЕ исходящие сообщения пользователю — с глобальным потолком по классу, приоритетом, дедупом и enforce предпочтений. Даёт обещанию-транспорту (DP.SC.134) зубы. | draft |
| DP.SC.178 | Голосовой канал IWE (Talk Mode — ввод) | SC | Голосовое сообщение в боте распознаётся в текст на границе канала; расшифровка всегда сохраняется как заметка (мысль не теряется), параллельно — best-effort ответ ассистента. Аудио не хранится, передача в Whisper раскрыта явно, отдельное согласие, текстовый fallback при сбое. | draft |
| DP.SC.179 | Семантический ревью соответствия инженерному стилю кода | SC | Контрольный агент по git diff семантически проверяет соответствие правилам инженерного стиля P1-P9 (DP.SC.172), которые механический детектор не ловит (копипаста P2, мёртвый код P3, смешение обязанностей P5, наблюдаемость P6, неидиоматичность P8, ручной парсинг P9). Выдаёт advisory-вердикт с file:line + severity в единый лог стиля → метрика code-compliance. Никогда не мутирует код; gating — политика потребителя, не роли. | active |
| DP.SC.180 | unit-economics | SC | — | draft |
| DP.SC.181 | Гард ID-коллизий в Pack-репо (pre-commit) | SC | Delta-aware pre-commit гард блокирует коммит, если новый entity-файл занимает уже существующий код (PREFIX.TYPE.N), и подсказывает следующий свободный номер. Закрывает гонку параллельных агентов, независимо берущих max+1 в одной рабочей папке. Глобальный pack-lint остаётся warning, CI check-pack-collisions — бэкстоп. | active |
| DP.SC.182 | Учётчик следов (trace-accountant) | SC | Принимает сырые следы от сенсорных адаптеров, проверяет consent, нормализует, маршрутизирует в домы знания по route_catalog, управляет stub-буфером offline+restrictive, ведёт reconciler-отчёт. Единственный authorized writer в learning.domain_event. | draft |
| DP.SC.183 | Bot Llm Dialog | SC | — | draft |
| DP.SC.184 | Bot Day Open | SC | — | draft |
| DP.SC.185 | Владелец тира (Tier Authority) | SC | Единственный authoritative-источник уровня доступа (traits.tier T0-T4). Вычисляет, хранит и поддерживает актуальность тира персоны по lifecycle-событиям: подписка, AI-клиент, GitHub, admin. | draft |
| DP.SC.186 | Bot Agent Session | SC | — | active |
| DP.SC.187 | local-gateway-render | SC | — | draft |
| DP.SC.188 | Синхронизация IWE-шаблона с англоязычной проекцией | SC | Каждое изменение README/docs личного русскоязычного шаблона IWE автоматически появляется переведённым в публичном английском репозитории iwesys/iwe-template — без участия автора и без отдельной команды на публикацию. | draft |
| DP.SC.189 | Зеркалирование методического контента aisystant в МИМ | SC | Каждое изменение в одном из 11 методических репозиториев aisystant (docs, guides, main-docs и т.д.) автоматически публикуется как есть (без перевода) в парном репозитории организации МИМ, без штатного git-механизма fork/transfer и без публичного отображения родства между организациями. | draft |
| DP.SOTA.001 | DDD Strategic (Khononov) | SOTA | Стратегический DDD: Bounded Context, Context Map, Ubiquitous Language — метод добычи и инженерной реализации доменного ядра | active |
| DP.SOTA.002 | Context Engineering | SOTA | Дисциплина курирования контекста ИИ-агента: Write/Select/Compress/Isolate — что попадает в окно, в каком формате, как обновляется | active |
| DP.SOTA.003 | Open API Specifications | SOTA | Экосистема открытых спецификаций интерфейсов: OpenAPI (sync), AsyncAPI (event-driven), CloudEvents (envelope) + Arazzo (workflows) | active |
| DP.SOTA.004 | GraphRAG + Knowledge Graphs | SOTA | Комбинация vector search + knowledge graph traversal для multi-hop reasoning: 87% vs 23% accuracy по сравнению с базовым RAG | active |
| DP.SOTA.005 | AI-Native Org Design | SOTA | Организационная архитектура для AI-first: плоские иерархии, agent orchestration, end-to-end accountability вместо функциональных силосов | active |
| DP.SOTA.006 | Agentic Development | SOTA | Multi-agent orchestration: инженеры оркестрируют специализированных агентов, а не пишут код напрямую; requirement-to-deploy за часы | active |
| DP.SOTA.007 | AI-Accelerated Ontology Engineering | SOTA | LLM ускоряют онтологическую инженерию в 10x: моделирование, расширение, population, alignment, entity disambiguation | active |
| DP.SOTA.008 | Real-Time Knowledge Capture | SOTA | Capture during work, not after: знания фиксируются в момент обнаружения, а не ретроспективно — консенсус KM 2026 | active |
| DP.SOTA.009 | Knowledge-Based User Models (Persona / Memory / Projection) | SOTA | Персональные/enterprise knowledge graphs и user models как трёхслойная архитектура: декларативная Персона (user-owned), наблюдаемая Память (platform-owned), runtime Проекция (ephemeral). Эволюция термина 'digital twin' в LLM-эру. | active |
| DP.SOTA.010 | DSL → DSLM Evolution | SOTA | Бифуркация DSL: классические domain-specific languages стабильны, фронтир ушёл в Domain-Specific Language Models (DSLM) | active |
| DP.SOTA.011 | Coupling Model (Khononov 2024) | SOTA | Многомерная модель связанности: knowledge coupling, distance coupling, volatility coupling — 4 уровня интеграции | active |
| DP.SOTA.012 | Multi-Representation Knowledge Architecture | SOTA | Model/View эволюционировал в multi-representation: vector + graph + hierarchical index, отделённые от surface (бот, курс, API) | active |
| DP.SOTA.013 | World Models | SOTA | Переход от LLM (модели знаний о мире) к World Models (модели мира): замыкание цикла действие-измерение-обновление, три линии исследований, архитектурные импликации для AI-агентов | active |
| DP.SOTA.014 | MCP как де-факто стандарт 2026 | SOTA | Model Context Protocol — универсальный стандарт подключения AI-агентов к enterprise-инструментам. 97M+ скачиваний SDK, 75+ коннекторов | active |
| DP.SOTA.015 | AI/LLM System Observability (3+1 Framework) | SOTA | SOTA-модель observability для AI/LLM: 3-сигнальная телеметрия (Traces/Metrics/Logs) + AI-специфичный слой Evaluations. «4-слойная AI observability» как именованный стандарт не существует. | draft |
| DP.SOTA.016 | Database-per-Service (паттерн изоляции данных) | SOTA | Каждый сервис владеет собственной базой данных. Схема ≠ изоляция. FK между сервисами заменяются API-контрактами или событиями. | active |
| DP.SOTA.017 | Концептуальные графы — мировой опыт | SOTA | Паттерны управления knowledge graphs: orphan-prevention, центральные узлы, многоязычность, editorial pipeline. Источники: OBO Foundry, Microsoft GraphRAG, Knowledge Space Theory (ALEKS), Wikidata. | active |
| DP.SOTA.018 | Управление терминологией в многоязычных онтологических системах | SOTA | Паттерны управления терминологией из ISO 704, SKOS, DDD UL и реальной практики крупных проектов — применимость к IWE | active |
| DP.SOTA.019 | Граф как runtime-инструмент агента + наблюдаемость | SOTA | Паттерны использования concept-графа агентом в runtime (Graph-RAG 2024-2026) + observability KG в продакшене + feedback loop от usage к эволюции графа. Дополняет DP.SOTA.004 (общая технология) и DP.SOTA.017 (структурная гигиена). | active |
| DP.SOTA.020 | Quantum-Like Modeling Lens (FPF C.26*, 2026) | SOTA | Математическая линза для систем с probe-coupled state change, order effects, incompatibility, false composition. QL-lite режим как дополнение к классическому набору, не замена. | active |
| DP.SOTA.021 | State-Based Management vs Task-List Management | SOTA | Управление через отслеживание состояний значимых объектов даёт измеримый эффект в системах с быстрой динамикой; task-list режим работает только при медленной реальности. Тест темпо-адекватности — критерий выбора. | active |
| DP.SOTA.022 | Agent Trace, Replay & Multi-Path Execution | SOTA | SOTA-обзор архитектурных паттернов для журнала решений LLM-агентов, повтора (replay) и параллельного многопутевого исполнения (multi-path / best-of-N). Дополняет DP.SOTA.015 (telemetry layer) — этот документ про rationale layer. | draft |
| DP.SOTA.023 | Инженерная семиотика — мировой опыт | SOTA | SOTA по инженерной семиотике для Pack-архитектуры IWE: триада Пирса, ISO 15926 (Kinds/Owner Roles), DDD Ubiquitous Language, OWL/SKOS. Что берём, что отвергаем, матрица применимости. | active |
| DP.SOTA.024 | BORO Methodology — Fundamental Particles & Fruitful Patterns | SOTA | SOTA-аннотация методологии BORO (Business Objects Re-Engineering for Re-Use, Partridge): фундаментальные онтологические частицы и гипотеза о межпроектной fruitfulness паттернов. trust: hypothesis. | active |
| DP.SOTA.025 | BORO — 4D Ontology & Naming Pattern | SOTA | SOTA-аннотация вклада BORO в 4D-онтологию (ISO 15926 family) и универсального naming-паттерна как framework-level reusable структуры. trust: hypothesis. | active |
| DP.SOTA.026 | Unified pipeline + content-hash skip — альтернатива дубль-pipeline для одного state | SOTA | Анти-паттерн: два кода (delta + full-rebuild) для одного derived state → drift risk. Паттерн: единая функция reindexFor(files[]) idempotent + content_hash skip → полный rebuild почти-нулевой стоимости; webhook / heartbeat-cron / manual вызывают одну точку. | draft |
| DP.SOTA.028 | Claude CLI headless hook inheritance — хуки из settings.json наследуются при `claude -p` | SOTA | Lifecycle-хуки Claude Code (PostToolUse, Stop из .claude/settings.json) срабатывают при `claude -p` идентично интерактивному режиму. Headless-агент автоматически получает весь hook-слой (WakaTime, agent-trace-recorder, rule-engine) без дополнительного кода, при условии что CLAUDE_CONFIG_DIR / CLAUDE_PROJECT_DIR указаны. | draft |
| DP.SOTA.029 | Ai Era Two Crisis Groups | SOTA | — | draft |
| DP.SOTA.030 | Eam Agent Manifest Standard | SOTA | — | draft |
| DP.SOTA.031 | Async Factory Deterministic Pipeline | SOTA | — | draft |
| DP.SOTA.032 | Semantic Chunking Rag | SOTA | — | draft |
| DP.SYS.001 | Детерминированные системы | SYS | Реестр детерминированных подсистем. Перенесено в DS-ecosystem-development → C2.IT-Platform | moved |
| DP.VM.001 | P1 P9 Calibration Matrix | VM | Девять промежуточных польз новичка: как система засекает достижение каждой (прокси/БД) и как Онбордер ведёт к ней (доставка/предусловие/характеристика Первокурсника/событие тира). | — |
| DP.WP.001 | Отчёт экстракции | WP | Структурированный отчёт экстракции знаний с классификациями, предложениями и валидацией | draft |
| DP.WP.002 | Ubiquitous Language | WP | Единый язык домена: глоссарий терминов, прорастающий во все артефакты — код, UI, документацию, тикеты, планы | draft |
| DP.WP.003 | DayPlan | WP | Ежедневный план работы: приоритеты, бюджеты, carry-over с предыдущего дня | draft |
| DP.WP.004 | WeekPlan | WP | Еженедельный план: итоги прошлой недели, РП текущей, бюджеты, контент-план, сверка со стратегией | draft |
| DP.WP.005 | WeekReport | WP | DEPRECATED: итоги недели теперь записываются в секцию «Итоги W{N-1}» внутри WeekPlan (DP.WP.004). Отдельный файл WeekReport больше не создаётся. | deprecated |
| DP.WP.006 | Fleeting Notes | WP | Быстрые заметки пользователя: мысли, задачи, наблюдения — сырьё для Note Review и экстракции | draft |
| DP.WP.007 | Consistency Report | WP | Отчёт проверки согласованности Pack-репо и downstream: расхождения, битые ссылки, дупликаты | draft |
| DP.WP.008 | Code Scan Report | WP | Ежедневный отчёт по коммитам за 24ч: репо, авторы, ключевые изменения, TG-нотификация | draft |
| DP.WP.009 | Unsatisfied Questions Report | WP | Еженедельный отчёт неудовлетворённых вопросов из feedback_triage DB: кластеры проблем, severity | draft |
| DP.WP.010 | CQRS Pack Projection | WP | YAML-проекция Pack frontmatter для knowledge-mcp: read-optimized view Pack-сущностей | draft |
| DP.WP.011 | Triage Backlog | WP | Приоритизированный backlog техдолга: баги, UX-проблемы, knowledge gaps — из Triage Session | draft |
| DP.WP.012 | Analytics Report | WP | Аналитический отчёт бота: метрики использования, тренды, качество ответов | draft |
| DP.WP.013 | Publication Schedule | WP | Расписание публикаций: посты со статусом ready → запланированные даты/время публикации в клубе | draft |
| DP.WP.014 | Validation Report | WP | Отчёт валидации: проверка шаблона экзокортекса (S24) или Pack-сущности (S38) на соответствие стандарту | draft |
| DP.WP.015 | WP-Registry | WP | Реестр всех рабочих продуктов (РП) стратегии: номер, название, статус — единое место для навигации по всей истории работы | draft |
| DP.WP.016 | Stage Dependency Map (Карта этапов с зависимостями) | WP | Формат рабочего продукта Аналитика ограничений (DP.ROLE.054): план работы по устранению ограничения, представленный как dependency graph без дат и часов. Узлы = этапы (внутри узла — параллельные работы и РП), рёбра = жёсткая зависимость («следующий этап начинается только после завершения предыдущего»), external-рёбра = зависимости от работ в других РП / репо. | draft |

> *Auto-generated by `generate-map.py` on 2026-07-10*
