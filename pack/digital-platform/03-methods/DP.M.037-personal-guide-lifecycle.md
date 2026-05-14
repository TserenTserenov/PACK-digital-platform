---
id: DP.M.037
title: "Lifecycle репо пилота personal-guide: 4 цикла с контрактами"
type: method
pack: digital-platform
status: draft
trust: 0.8
epistemic_stage: observed
valid_from: 2026-05-13
related: [DP.IWE.002, DP.IWE.006]
---

# DP.M.037 — Lifecycle репо пилота personal-guide

## Описание

Метод управления жизненным циклом персонального репо пилота (`personal-guide`), синхронизированного с платформой через GitHub App. Четыре цикла с явными контрактами.

## Циклы

| Цикл | Триггер | Контракт (SLA) |
|------|---------|----------------|
| **Reindex** | git push в репо | ≤30 сек до обновления индексов |
| **История** | Пересборка руководства | Архивация в history/ на шаге 5 render |
| **Skill distribution refresh** | Повторный render ≥30 дней | Checksum-based: обновление только при изменении |
| **GDPR right-to-delete** | Запрос пилота | ≤72 часа: 4 шага |

## GDPR Right-to-Delete (4 шага)

1. Uninstall GitHub App
2. Delete repo
3. Purge indexes (embeddings, search index)
4. Delete webhook records

## Инварианты

- GDPR-flow разделяет **Персону** (репо + embeddings) и **Память.Derived** (user_events в Neon) — удаляются отдельно.
- Lifecycle-spec явно указывает, что **НЕ удаляется автоматически** (Neon events требуют отдельного запроса).
- Checksum-based refresh предотвращает лишние rebuild при повторных render'ах.

## Применимость

Паттерн применим к любому пользовательскому репо, синхронизированному с платформой через GitHub App. Ключевое: lifecycle-spec должен явно называть, что НЕ удаляется автоматически.

## Связи

- Сущность: DP.IWE.006 Personal Guide Channels
- Template: DP.IWE.002 (IWE template/setup)
- WP: WP-309 Ф2-Ф6
