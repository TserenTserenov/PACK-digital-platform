---
id: DP.METHOD.180
title: "Pre-promotion peer-gate"
type: METHOD
domain: digital-platform
status: candidate
trust: low
epistemic_stage: candidate
valid_from: 2026-07-13
source: "session-close 2026-07-09, peer-сессия IDCOL1 prod verify gate (WP-7 фаза IDCOL1)"
tags: [peer-review, production-deployment, verification, irreversible-actions]
---

# [DP.METHOD.180] Pre-promotion peer-gate

## Описание

Обязательная peer-сессия перед необратимым действием в прод (production migration, schema change). Напарник проверяет **correctness допущений фазы**, а не стиль кода.

## Отличие от code review

| | Code review | Pre-promotion peer-gate |
|---|---|---|
| Когда | После написания кода | Перед необратимым действием |
| Что проверяет | Стиль, логику, тесты | Correctness допущений фазы |
| Эффект | Post-factum review | Блокирует ошибочный деплой |

## Алгоритм (IPO)

**Вход:** план прогона + текущее состояние системы

**Действия напарника:**
1. Читает план независимо от автора
2. Задаёт минимальный чеклист: (а) какой код идёт в прод — disk vs git? (б) есть ли timeout? (в) правильный ли тип данных/колонок? (г) актуален ли статус ветки/PR?
3. Запускает верификационный прогон (dry-run или staging)
4. Оценивает результат: **PASS** / **BLOCK** с перечнем найденных расхождений

**Выход:** go/no-go решение с обоснованием

## Критерий применения

Применять когда:
- Действие необратимо (production migration, force-push, schema drop)
- Автор работал с кодом >2 часов подряд (tunnel vision risk)
- Основное допущение фазы не было независимо проверено

## Пример применения

IDCOL1 prod verify gate (WP-7, 2026-07-09): напарник зафиксировал 4 риска; прогон показал BLOCK — 134 расхождения, основное допущение перевёрнуто (133 реальных пользователя потеряли бы привязку). Без peer-gate дефект ушёл бы в прод.

## Связи

- DP.METHOD.173 (context-isolated verifier — верификация через изолированного субагента)
- DP.METHOD.177 (sequential-verification-rounds — последовательные раунды верификации)
