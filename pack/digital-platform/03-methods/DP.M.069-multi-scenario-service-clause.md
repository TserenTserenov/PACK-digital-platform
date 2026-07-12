---
id: DP.M.069
name: Multi-scenario Service Clause — одно обещание, N delivery-сценариев
kind: Method
status: active
created: 2026-05-17
sources:
  - PACK-digital-platform DP.SC.139 (IWE Stage Controller) — commits 61bf40e, a448b9a, cea988d
related:
  complements: [DP.D.064]
  applies_to: [SC-design, IntegrationGate phase 1]
---

# DP.M.069: Multi-scenario Service Clause

## Определение

Один SC = одно обещание потребителю, но возможны **несколько delivery-сценариев** (A / B / C / …) с разными SLA, режимами и channel'ами доставки. Все сценарии реализуют **то же обещание**.

## Канонические delivery-сценарии

| Сценарий | Назначение | Триггер |
|----------|-----------|---------|
| **A — primary** | Основной путь доставки | штатный таймер / событие |
| **B — preview / inspection** | Просмотр результата до основной доставки | manual call оператором |
| **C — retry / degraded** | Восстановление после сбоя primary | failure detection + backoff |

Список не исчерпывающий — могут быть и другие (manual override, multi-channel fan-out).

## Тест корректности multi-scenario SC

| Вопрос | Ответ → решение |
|--------|----------------|
| «Есть ли в SC одно обещание, но >1 сценарий выполнения?» | Да → multi-scenario SC корректен |
| «Есть ли разные обещания внутри одного SC?» | Да → разделить на N отдельных SC |
| «Один сценарий = одно обещание?» | Да → потерян degraded-режим, объединить или явно описать инвариант failure |

## Прецедент

**DP.SC.139** (IWE Stage Controller): обещание — «доставить пилоту блок “Стадия мастерства IWE” из profile.md».

- **Сценарий A:** primary — рассылка по таймеру в 05:30 МСК.
- **Сценарий B:** preview-TG для предварительного просмотра пилотом.
- **Сценарий C:** retry с backoff при ошибке primary.

Все три реализуют одно обещание, отличаются только delivery-path и SLA.

## Антипаттерн

- **Отдельный SC на каждый сценарий.** Инфляция каталога (DP.SC.139A, .139B, .139C), потеря целостности обещания.
- **Один сценарий = одно обещание.** Теряется degraded-режим, при сбое primary потребитель остаётся без gracefulness.

## Применимо к

- Любой SC с graceful degradation / retry policy.
- Preview-режимы (TG preview, dry-run UI).
- Multi-channel delivery (TG + email + UI fan-out на тот же контент).

## Различение

- **[[DP.D.064]] Same vs different promise WP branch:** про РП-уровень (когда фаза становится отдельным РП по различию promise). Этот метод — про **SC-design внутри одного обещания**.
- **«SC vs Carrier» DP.D.058:** разные роли — обещание vs его носитель. Multi-scenario SC — про **внутреннюю структуру самого обещания**.

## Источник

DP.SC.139 (IWE Stage Controller, WP-326) — первый прецедент в Pack явно описанной multi-scenario структуры. Зафиксировано 2026-05-17.
