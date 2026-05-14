---
id: DP.IWE.006
title: "Personal Guide Channels — матрица 3 канала × 4 операции"
type: entity
pack: digital-platform
status: draft
trust: 0.8
epistemic_stage: observed
valid_from: 2026-05-13
related: [DP.IWE.001, DP.IWE.004, DP.M.037]
---

# DP.IWE.006 — Personal Guide Channels

## Описание

Архитектура доступа к персональному руководству пилота из разных клиентских сред. Один репо `personal-guide` — три интерфейса с разным набором поддерживаемых операций.

## Матрица: 3 канала × 4 операции

| Операция | Бот (Telegram) | Браузер (Web) | VS Code |
|----------|:--------------:|:-------------:|:-------:|
| Create репо | ✅ | ✅ | ✅ |
| Read руководство | ✅ | ✅ | ✅ |
| Commit reflection | ✅ | ❌ | ✅ |
| Refresh on RCS change | ❌ | ❌ | ✅ |

## Сценарии

- **Мобильный:** бот — нет VS Code; доступны: Create + Read + Commit
- **Чужой ноутбук:** браузер — доступны: Create + Read
- **Рабочее место:** VS Code — все операции

## Инвариант

Репо `personal-guide` функционирует одинаково из всех трёх каналов для операций, поддерживаемых данным каналом. Матрица — SoT для проектирования клиентских интерфейсов.

## Применимость

Паттерн «один артефакт — несколько каналов» применим к любому персональному артефакту (workbook, portfolio, roadmap), требующему доступа из разных клиентских окружений.

## Связи

- Lifecycle: DP.M.037 Personal Guide Lifecycle
- Интерфейсы IWE: DP.IWE.004 IWE Interfaces
- WP: WP-309 Ф2
