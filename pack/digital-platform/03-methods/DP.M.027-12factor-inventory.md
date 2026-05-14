---
id: DP.M.027
name: "12-factor Matrix для инвентаризации production deployment"
name_ru: "Матрица 12-factor для инвентаризации продакшена"
name_en: "12-factor Matrix for Production Inventory"
type: method
status: active
summary: "Метод систематической инвентаризации всех production deployment units через матрицу F1-F12. Позволяет обнаруживать системные дефекты (например, floating deps у всех Python-сервисов) за один проход по стеку."
created: 2026-05-12
trust:
  F: 3
  G: applied
  R: 0.85
epistemic_stage: applied
tags: [deployment, 12factor, inventory, platform, infra, compliance]
wp: WP-307
---

# 12-factor Matrix для инвентаризации production deployment (DP.M.027)

## Описание

Метод применяет 12-factor methodology как матрицу для полного аудита всех production deployment units платформы. В отличие от одиночного 12-factor compliance check, метод охватывает весь стек одновременно и выявляет системные дефекты: если один фактор F_N ❌ у 10+ сервисов — это системная проблема, закрываемая одним РП.

## Алгоритм

### Шаг 1: Реестр deployment units

Составить список ВСЕХ production units с уникальными кодами. Включать все типы:

- Боты (B1, B2...)
- Воркеры (W1, W2...)
- Системы и ML-сервисы (M1-M11, L1, O1...)
- Агенты (A1-A6...)
- Edge-функции CF Workers (X1-X3...)
- Прочие (P1, T1, AD1...)

### Шаг 2: Матрица F1-F12

Для каждого unit заполнить таблицу по 12 факторам:

| Unit | F1 | F2 | F3 | F4 | F5 | F6 | F7 | F8 | F9 | F10 | F11 | F12 |
|------|----|----|----|----|----|----|----|----|----|----|-----|-----|
| B1   | ✅ | ⚠️ | ✅ | ? | ... | | | | | | | |

Статусы: ✅ = соответствует / ⚠️ = частично / ❌ = не соответствует / ? = неизвестно.

12 факторов (краткое название): F1 Codebase, F2 Dependencies, F3 Config, F4 Backing Services, F5 Build/Release/Run, F6 Processes, F7 Port Binding, F8 Concurrency, F9 Disposability, F10 Dev/Prod Parity, F11 Logs, F12 Admin Processes.

### Шаг 3: Поиск системных дефектов

Просмотреть каждый фактор по вертикали. Признак системного дефекта: F_N ❌ у 3+ сервисов. Приоритизация: (число затронутых) × (критичность фактора).

### Шаг 4: Группировка в РП

Один системный дефект = один РП (не N РП по числу сервисов). Например: F2 ❌ у всех Python-сервисов → один РП «Dependency lock-файлы для Python-сервисов».

## Инварианты

- Охватывать ВСЕ production units: не только основные боты и воркеры
- Включать разные типы deployment в один реестр (не отдельные аудиты)
- Пустая строка матрицы = ? = риск, приравнивается к ⚠️
- Аудит повторять при масштабировании стека (> 30% новых units)

## Источник

WP-307 (12-factor compliance audit, 12 мая 2026). Применён к 31 production unit (B1-B2, W1-W5, M1-M11, L1, O1, A1-A6, X1-X3, P1, T1, AD1). Критический риск найден: F2 ❌ у всех Python-сервисов (floating deps: `aiogram>=`, `langfuse>=`).

## Связи

- Метод основан на: 12factor.net (Adam Wiggins, Heroku, 2011)
- DP.M.008 (IWE operating rules) — смежный метод управления платформой
- DP.FM.006 (когнитивный долг) — аудит через 12-factor снижает когнитивный долг
