---
id: DP.M.002
name: Применение стратегического DDD
type: method
status: draft
summary: "Метод применения стратегического DDD к Pack и экзокортексу: BC mapping, UL extraction, Context Map для inter-agent integration"
created: 2026-02-13
last_updated: 2026-07-27
trust:
  F: 3
  G: domain
  R: 0.5
related:
  uses: [DP.SOTA.001, DP.SOTA.011, DP.D.017]
  produces: [DP.WP.002]
  integrates_with: [DP.M.001]
tags: [ddd, bounded-context, method, ubiquitous-language, context-map]
---

# Применение стратегического DDD

## 1. Определение

Метод систематического применения стратегического DDD (Bounded Context, Context Map, Ubiquitous Language) к проектированию Pack, агентов и межсистемных интеграций экзокортекса.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Точность Bounded Context ↔ скорость запуска Pack | Строгая проверка truth criteria и вопроса «нет ли двух BC в одном Pack» (§3.1) замедляет старт, но пропуск этого шага откладывает конфликт на потом — когда разделение Pack'а на два BC обойдётся дороже, чем на старте |
| Полнота Ubiquitous Language ↔ поддерживаемость глоссария | Сбор терминов из ВСЕХ источников (§3.2: код, UI, документация, тикеты, диалоги) даёт полное покрытие, но каждый новый источник — это нагрузка на поддержку; глоссарий либо остаётся неполным, либо превращается в артефакт, который никто не обновляет после первого прохода |
| Строгость Context Map паттернов ↔ риск overhead | Явный выбор среди 4 паттернов интеграции (§3.3: Published Language, ACL, Shared Kernel, Customer-Supplier) вынуждает осознанное решение вместо случайной связи, но для мелкой/неформальной интеграции это добавляет церемонию там, где хватило бы прямой ссылки |

## 2. IPO-паттерн

| Элемент | Описание |
|---------|----------|
| **Входы** | Доменное знание (текст, диалоги, код) + существующие Pack entities |
| **Обработка** | BC identification → UL extraction → Context Map creation |
| **Выходы** | Обновлённый bounded context, UL (DP.WP.002), Context Map |

## 3. Шаги метода

### 3.1. Bounded Context Identification

При создании нового Pack или агента:
1. Определить объект описания (что моделируем?)
2. Определить scope (что внутри, что вне?)
3. Написать truth criteria (когда модель верна?)
4. Проверить: нет ли двух BC в одном Pack? (→ Sub-Pack)

### 3.2. Ubiquitous Language Extraction

1. Собрать термины из всех источников (код, UI, документация, тикеты, диалоги)
2. Проверить: один термин = одно значение ВЕЗДЕ?
3. Зафиксировать в ontology.md как глоссарий
4. Рабочий продукт: DP.WP.002 (Ubiquitous Language)

### 3.3. Context Map Creation

Определить интеграции между bounded contexts:

| Pattern | Когда | Пример |
|---------|-------|--------|
| **Published Language** | Контексты общаются через общий формат | Pack frontmatter schema |
| **Anti-Corruption Layer** | Защита от чужой модели | Bot → Pack adapter |
| **Shared Kernel** | Общая часть модели | SPF shared types |
| **Customer-Supplier** | Один контекст зависит от другого | Downstream → Pack |

### 3.4. НЕ применять

- Entity, Value Object, Aggregate — тактический DDD, ООП-специфика (DP.D.017)
- Repository pattern — не нужен при файловой организации Pack
- Domain Events в коде — рассматривать только при event-driven архитектуре

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative` по прецеденту SA.METHOD.001 (WP-448 Ф12)._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Смешение стратегического и тактического DDD | Внимание съезжает на знакомый тактический словарь (Entity, Aggregate, Repository) даже после явного запрета в §3.4 — привычные паттерны кода перетягивают фокус с уровня Bounded Context/Context Map на уровень классов, хотя метод строго про стратегический слой |
| _(tentative)_ Ubiquitous Language как разовый артефакт | После первого прохода §3.2 внимание переключается на следующую задачу — глоссарий недооценивается как живой процесс и не обновляется при появлении новых терминов в новых источниках |

## 4. Связанные документы

- [DP.SOTA.001](../06-sota/DP.SOTA.001-ddd-strategic.md) — SOTA-источник
- [DP.WP.002](../04-work-products/DP.WP.002-ubiquitous-language.md) — рабочий продукт
- [DP.D.017](../01-domain-contract/01B-distinctions.md) — различение стратегический vs тактический DDD

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (пилотный прогон). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
