---
id: DP.SOTA.001
name: DDD Strategic (Khononov)
type: sota
status: active
summary: "Стратегический DDD: Bounded Context, Context Map, Ubiquitous Language — метод добычи и инженерной реализации доменного ядра"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.8
related:
  enables: [DP.M.002]
  integrates_with: [DP.SOTA.002, DP.SOTA.011]
  supersedes_partially: [DP.SOTA.010]
tags: [ddd, bounded-context, ubiquitous-language, domain-modeling, khononov]
---

# DDD Strategic (Khononov)

## 1. Определение

**Domain-Driven Design (стратегический)** — метод добычи и формализации доменного знания через три ключевых инструмента: Bounded Context (граница ответственности модели), Context Map (карта интеграции контекстов), Ubiquitous Language (единый язык домена, прорастающий везде).

> FPF-позиционирование: FPF → метапринципы. SPF → спецификация ядра. DDD → метод добычи и инженерной реализации ядра.

## 2. Статус SOTA (февраль 2026)

**SOTA, эволюционирует.** DDD обрёл новую релевантность через два вектора:

- **DDD + Agentic AI:** bounded contexts мапятся на multi-agent архитектуры — каждый агент работает в своём BC с собственными моделями и UL.
- **Khononov 2024:** книга «Balancing Coupling in Software Design» расширяет DDD моделью coupling (knowledge/distance/volatility) — см. DP.SOTA.011.

## 3. SPF-интеграция (10 слотов)

| SPF-слот | Что добавляет DDD |
|----------|-------------------|
| Bounded Context | Context Mapping, ACL, Published Language |
| Словарь (Kinds, термины) | Ubiquitous Language |
| Инварианты / вторые принципы | Aggregates (только стратегические) |
| Boundary Statements | ACL, Published Language, Domain Events |
| Multi-View представления | Domain/Application/Infrastructure layers |
| Проекция в CodeView | Tactical DDD (ограниченно) |
| Интеграция контекстов | Context Mapping Patterns |
| Evidence / Assurance | Domain tests, event-driven проверки |
| Эволюция модели | Refactoring toward deeper insight, core/supporting/generic |
| Разделение политики и механизма | Layered Architecture |

## 4. Правила применения

1. **При создании Pack** — использовать DDD Bounded Context как основу для scope Pack.
2. **При определении словаря** — проверять через UL: используется ли термин одинаково во всех контекстах (код, UI, документация, тикеты)?
3. **При интеграции систем** — применять Context Mapping Patterns для определения границ ответственности.
4. **НЕ применять тактический DDD** (Entity, Value Object, Aggregate) как обязательные паттерны — это ООП-специфика, не универсальна (см. DP.D.017).

## 5. Ключевые различения

- Стратегический DDD (SOTA) ≠ Тактический DDD (ООП-карго-культ) → DP.D.017
- Модель ≠ Код: модель — формализация из предметной области, код — исполняемое представление
- Цель DDD — вернуть разработку в реальный мир, не сделать код моделью

## 6. Источники

- Khononov V. «Learning Domain-Driven Design» (O'Reilly, 2021)
- Khononov V. «Balancing Coupling in Software Design» (Pearson, 2024)
- Evans E. «Domain-Driven Design» (Addison-Wesley, 2003) — оригинал
- SE Radio 662: Khononov on Balancing Coupling (April 2025)
