id: DP.M.221
type: method
domain: digital-platform
slug: infrastructure-snapshot-living-artifact
title: Инфраструктурный snapshot как живой операционный артефакт
status: draft
created: 2026-05-29
source: session-transcript 2026-05-29 (WP-341)
trust: personal-observation
epistemic_stage: observed
---

# DP.M.221 — Инфраструктурный snapshot как живой операционный артефакт

## Описание

Для каждой функциональной подсистемы платформы вести `*-infrastructure.md` с тремя разделами. Документ — живой операционный артефакт, не архивная документация.

## Структура документа

1. **Карта сервисов** — таблица: сервис | статус | URL | хостинг | роль
2. **Схема мониторинга и алертинга** — каналы, пороги, ответственность
3. **Roadmap развития** — что запланировано, в каком горизонте

## Применение

1. Создать `{subsystem}-infrastructure.md` при выводе подсистемы в production
2. Обновлять инкрементально при каждом изменении (новый сервис, смена хостинга, изменение мониторинга)
3. Не создавать отдельный snapshot в архиве — обновлять один файл

## Пример

`WP-341-helpdesk-infrastructure.md` (230 строк):
- Сервисы: Railway (helpdesk), BetterStack (monitoring), Telegram (alerts), Neon (DB)
- Мониторинг: `/healthz` эндпоинт, каналы оповещения
- Roadmap: keyword-check, internal probe hardening

## Отличие от архивного документа

Архивный snapshot: фиксирует состояние на момент написания, не обновляется.
Живой артефакт: эволюционирует с системой, является source-of-truth текущего состояния.
~~~

**Вердикт:** accept
**Обоснование:** Переносимый метод документирования (не vendor-specific, не implementation-specific). Применим к любой подсистеме платформы. Не дублирует существующие DP.M.*. Bounded context PACK-digital-platform: метод проектирования платформы — корректно.

---

## Сводка

| Метрика | Значение |
|---------|----------|
| Captures обработано | 3 |
| Всего кандидатов | 3 |
| Accept | 3 |
| Reject | 0 |
| Defer | 0 |
| Осталось в inbox | 0 |
