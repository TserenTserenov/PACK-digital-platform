# Pack Manifest: ИТ-платформа и цифровой двойник

## Идентификатор

- **Pack ID**: `DP`
- **Версия**: 0.2.0
- **Статус**: Draft

## Область

**Название**: ИТ-платформа и цифровой двойник

**Описание**: Предметная область ИТ-платформы экосистемы развития интеллекта: архитектура платформы (4 слоя, 3 характеристики), ИИ-системы (агенты + ассистенты), цифровой двойник созидателя, навигация знаний между репозиториями, модульный экзокортекс пользователя, метод экстракции знаний, IPO-паттерн компонентов, отчуждаемость (platform-space vs user-space).

## Scope

### В scope

- Архитектура платформы (4-слойная модель)
- Архитектурные характеристики (эволюционируемость, масштабируемость, обучаемость)
- ИИ-системы платформы (реестр, классификация, паспорта)
- Цифровой двойник как модель созидателя
- Различения объект/модель/данные/представление + доменные различения
- Навигация знаний (4 уровня, кросс-репо ссылки)
- Модульный экзокортекс (CLAUDE.md + Memory шаблоны, 3-слойная архитектура инструкций)
- Метод экстракции знаний (информация → Pack-совместимые сущности)
- IPO-паттерн как универсальный контракт компонентов
- Отчуждаемость: platform-space vs user-space
- Индикаторы и их типы
- Детерминированные системы и MCP-серверы
- Типовые ошибки (failure modes)

### Вне scope

- Код MCP-сервиса (это digital-twin-mcp — downstream/instrument)
- Код бота (это aist_bot — downstream/instrument)
- Код Стратега (это strategist-agent — downstream/instrument)
- UI/UX (это downstream/surface)
- Содержание индикаторов созидателя (это spf-personal-pack)
- Знания об экосистеме (это spf-ecosystem-pack)
- Планы, сроки, реестры проекта (это ecosystem-development — downstream/governance)

## Расширенные виды (SPF.SPEC.001)

> Доменные виды сущностей, определённые этим Pack. Базовые виды (M, WP, FM, D, R, CHR, SOTA, MAP) определены в SPF.

| Код | Вид | Описание | Папка |
|-----|-----|----------|-------|
| `ARCH` | Architecture | Архитектурное описание системы | `02-domain-entities/` |
| `CONCEPT` | Concept | Концепция системы/подсистемы | `02-domain-entities/` |
| `SYS` | System | Детерминированная подсистема (сервис, MCP) | `02-domain-entities/` |
| `AGENT` | AI Agent | Реестр / паспорт ИИ-системы | `02-domain-entities/` |
| `AISYS` | AI System | Конкретная ИИ-система (паспорт) | `02-domain-entities/` |
| `NAV` | Navigation | Навигационная структура знаний | `02-domain-entities/` |
| `EXOCORTEX` | Exocortex | Модульный экзокортекс пользователя | `02-domain-entities/` |

## Содержимое Pack'а

| Категория | Файлов | Ключевые сущности |
|-----------|--------|-------------------|
| Доменный контракт | 3 | 15 различений, bounded context, онтология (SPF.SPEC.002) |
| Доменные сущности | 8+1 | DP.ARCH.001, DP.AGENT.001, DP.AISYS.013, DP.NAV.001, DP.EXOCORTEX.001, DP.AGENT.012, DP.SYS.001, DP.CONCEPT.001 |
| Методы | 1 | DP.M.001 (экстракция знаний) |
| Рабочие продукты | 1 | DP.WP.001 (отчёт экстракции) |
| Failure modes | 2 | DP.FM.001 (информация как знание), DP.FM.002 (смешение слоёв) |
| Карта | 1 | DP.MAP.001 (навигационная карта) |

> Полная карта: [07-map/DP.MAP.001.md](07-map/DP.MAP.001.md)

## Upstream dependencies

- [TserenTserenov/SPF](https://github.com/TserenTserenov/SPF) — Second Principles Framework
- [ailev/FPF](https://github.com/ailev/FPF) — First Principles Framework
- [aisystant/spf-personal-pack](https://github.com/aisystant/spf-personal-pack) — контракт индикаторов созидателя

## Downstream outputs

- [digital-twin-mcp](https://github.com/aisystant/digital-twin-mcp) — MCP-реализация цифрового двойника (instrument)
- [aist_bot](https://github.com/aisystant/aist_bot) — Telegram-бот, тонкий клиент (instrument)
- [strategist-agent](https://github.com/TserenTserenov/strategist-agent) — Агент Стратег (instrument)
- [extractor-agent](https://github.com/TserenTserenov/extractor-agent) — Знание-Экстрактор, prompt-based ИИ-система (instrument)
- [exocortex-setup-agent](https://github.com/TserenTserenov/exocortex-setup-agent) — Агент развёртывания экзокортекса (instrument)
- [exocortex-template](https://github.com/TserenTserenov/exocortex-template) — Шаблон экзокортекса (format)
- [my-strategy](https://github.com/TserenTserenov/my-strategy) — Личный стратегический хаб (governance)

## Maintainers

- @TserenTserenov

## Changelog

- 0.2.0 — Архитектурное обновление: 4-слойная модель, ИИ-системы (объединение агентов/ассистентов), 3 характеристики (эволюционируемость, масштабируемость, обучаемость), IPO-паттерн, отчуждаемость, навигация знаний, модульный экзокортекс, метод экстракции знаний, 6 новых различений, failure modes, навигационная карта
- 0.1.0 — Initial structure
