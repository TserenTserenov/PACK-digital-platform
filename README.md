# spf-digital-platform-pack

> **Тип репозитория:** `Pack`

> Pack (паспорт области) для предметной области «ИТ-платформа и цифровой двойник»

## Что это

Это **source-of-truth** для области «ИТ-платформа и цифровой двойник» — технического слоя экосистемы развития интеллекта, включая знание о цифровом двойнике созидателя.

## Тип репозитория

- **Тип**: Pack
- **Source-of-truth**: Да
- **Область**: ИТ-платформа и цифровой двойник

## Структура

```
spf-digital-platform-pack/
├── README.md
├── REPO-TYPE.md
└── pack/
    └── digital-platform/
        ├── 00-pack-manifest.md        # Метаданные pack'а
        ├── 01-domain-contract/        # Контракт домена
        │   ├── 01A-bounded-context.md
        │   └── 01B-distinctions.md
        ├── 02-domain-entities/        # Сущности домена
        ├── 03-methods/                # Методы валидации
        ├── 04-work-products/          # Рабочие продукты
        ├── 05-failure-modes/          # Типовые ошибки
        ├── 06-sota/                   # SoTA-аннотации
        └── 07-map/                    # Карты связей
```

## Ключевые понятия

| Понятие | Описание |
|---------|----------|
| **Цифровой двойник** | Вычислительная модель созидателя |
| **Объект** | Реальный созидатель |
| **Модель** | Формализованное описание объекта |
| **Данные** | Значения индикаторов |
| **Представление** | Визуализация для пользователя |
| **Индикатор** | Наблюдаемый/вычисляемый показатель |

## Ключевые различения

- **Объект ≠ Модель**: Модель — упрощение реальности
- **Модель ≠ Данные**: Структура vs значения
- **Данные ≠ Представление**: Внутреннее vs внешнее
- **Генеративный текст ≠ Source-of-truth**: ИИ-интерпретации не являются истиной

## Upstream

- [TserenTserenov/SPF](https://github.com/TserenTserenov/SPF) — Second Principles Framework
- [ailev/FPF](https://github.com/ailev/FPF) — First Principles Framework
- [aisystant/spf-personal-pack](https://github.com/aisystant/spf-personal-pack) — контракт индикаторов

## Downstream

- [digital-twin-mcp](https://github.com/aisystant/digital-twin-mcp) — MCP-реализация
- [aist_bot](https://github.com/aisystant/aist_bot) — Telegram-бот

## Non-goals

- НЕ содержит кода (это downstream-instrument)
- НЕ содержит UI/UX (это downstream)
- НЕ определяет индикаторы созидателя (это spf-personal-pack)

---

*Pack построен по SPF (Second Principles Framework)*
