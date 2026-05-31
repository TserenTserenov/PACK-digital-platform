---
id: DP.M.252
name: Satisfied-by-Existing-Content — pre-build scout как класс defer в delivery pipeline
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
source: DS-my-strategy session-close 2026-05-31, peer-19 report.md Ф-E, commit 2d6e1d1d
---

# DP.M.252 — Pre-build scout: класс defer «satisfied-by-existing-content»

## Суть

В multi-WP delivery pipeline перед реализацией новой фазы обязателен pre-build scout:
существует ли контент/артефакт, который уже закрывает scope на ≥80%?
Если да → defer с маркером `satisfied-by-existing-content`, не строить.

## Три класса defer в delivery pipeline

| Класс | Маркер | Триггер |
|-------|--------|---------|
| Blocked by external state | `deferred-until-channel-active` | внешняя зависимость не готова |
| Existing content suffices | `satisfied-by-existing-content` | существующий контент перекрывает ≥80% scope |
| Needs architecture review | `requires-archgate` | изменение контракта/инварианта |

## Алгоритм pre-build scout

1. Перед реализацией фазы: определить scope (что должно быть создано/изменено)
2. Провести grep/audit существующего контента по ключевым концептам
3. Если покрытие ≥80% → отметить `satisfied-by-existing-content` + ссылку на существующий источник
4. Если < 80% → реализовывать

## Пример

Ф-E (создать секцию pro-guides для routing) — обнаружена aisystant/docs/ru/professional/ с 896 .md.
Scope перекрыт → defer с `satisfied-by-existing-content`, не создавать дублирующий контент.

## Применимость

Любой delivery pipeline с созданием контента, сервисов, документации:
сначала grep/audit existing, потом build.

## Связи

- DP.M.249 (delivery-tracker-umbrella-wp) — смежный паттерн управления delivery
