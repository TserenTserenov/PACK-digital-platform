# Pack Manifest: ИТ-платформа и цифровой двойник

## Идентификатор

- **Pack ID**: `DTP`
- **Версия**: 0.1.0
- **Статус**: Draft

## Область

**Название**: ИТ-платформа и цифровой двойник

**Описание**: Предметная область ИТ-платформы экосистемы развития интеллекта, включая цифровой двойник созидателя: различения объект/модель/данные/представление, характеристики качества двойника, методы валидации, типовые ошибки интерпретации.

## Scope

### В scope

- Цифровой двойник как модель созидателя
- Различение объект/модель/данные/представление
- Индикаторы и их типы
- Методы валидации модели
- Характеристики качества двойника
- Типовые ошибки интерпретации данных
- Контракт с downstream (MCP, бот)

### Вне scope

- Код MCP-сервиса (это digital-twin-mcp)
- Код бота (это aist_bot)
- UI/UX (это downstream)
- Содержание индикаторов созидателя (это spf-personal-pack)

## Upstream dependencies

- [TserenTserenov/SPF](https://github.com/TserenTserenov/SPF) — Second Principles Framework
- [ailev/FPF](https://github.com/ailev/FPF) — First Principles Framework
- [aisystant/spf-personal-pack](https://github.com/aisystant/spf-personal-pack) — контракт индикаторов созидателя

## Downstream outputs

- [digital-twin-mcp](https://github.com/aisystant/digital-twin-mcp) — MCP-реализация
- [aist_bot](https://github.com/aisystant/aist_bot) — Telegram-бот

## Maintainers

- @TserenTserenov

## Changelog

- 0.1.0 — Initial structure
