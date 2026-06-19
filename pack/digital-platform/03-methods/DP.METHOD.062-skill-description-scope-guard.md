---
id: DP.METHOD.062
title: "Skill description scope guard: что входит и что НЕ входит в SKILL.md"
type: method
pack: DP
tags: [skill, skill-md, scope-guard, what-not-to-include, platform-skill, knowledge-routing]
status: draft
valid_from: 2026-06-16
schema_version: 1
---

# DP.METHOD.062 — Skill description scope guard: что входит в SKILL.md, что нет

## Назначение

SKILL.md описывает контракт (обещание + алгоритм) специализированного умения агента. Этот scope guard определяет, что в SKILL.md должно быть, что не должно — и куда маршрутизировать остальное.

## Что входит в SKILL.md

| Блок | Назначение |
|------|-----------|
| `## When to use` | Когда использовать скилл (триггеры, scope, NOT) |
| `## Algorithm` | Пошаговый алгоритм (IPO) |
| `## Appendix` | Справочные данные (таблицы, форматы) |

## Что НЕ входит в SKILL.md (куда маршрутизировать)

| Что НЕ входит | Куда |
|---------------|------|
| Доменное знание (что такое Pack, как работает KE) | Pack-файлы (DP.D.*, DP.METHOD.*) |
| Архитектурные решения (почему так, а не иначе) | DP.ARCH.NNN или пир-сессия report.md |
| Истории сессий / changelog | sessions/*.md |
| Временные договорённости / defer-списки | inbox/extraction-reports/*.md |
| Платформенные правила (CLAUDE.md-уровень) | CLAUDE.md или PACK-agent-rules |

## Тест применимости

«Если удалить этот блок из SKILL.md — алгоритм скилла всё ещё понятен агенту без него?» Да → блок в SKILL.md не нужен, маршрутизировать в Pack / sessions.

## Связь с DP.D.147

Scope guard задаёт «минимальный содержательный барьер» (DP.D.147): SKILL.md проходит если все три обязательных секции присутствуют с содержательным наполнением. Инфраструктурная оболочка без алгоритма = не проходит.

## Источник

session-transcript 2026-06-16; WP-422 Ф7 (SKILL.md review, content-audit.sh design)
