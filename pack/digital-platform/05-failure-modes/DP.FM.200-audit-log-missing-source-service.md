---
id: DP.FM.200
title: "Аудит-лог без атрибуции source_service: невозможно установить источник записи при расследовании"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / audit-logging
epistemic_stage: confirmed
valid_from: 2026-07-04
source: "session-close 2026-07-04 (WP-457 Ф10)"
related:
  see_also: [DP.FM.091]
---

# DP.FM.200 — Аудит-лог без атрибуции source_service: невозможно установить источник записи при расследовании

## Описание

Таблица аудит-журнала не содержит поля, однозначно идентифицирующего сервис-источник записи. В multi-service архитектуре несколько сервисов пишут в одну таблицу; при отсутствии `source_service` строки аудита не атрибутируемы — расследование инцидента требует внешних артефактов (логи сервисов, трейсы).

## Пример

WP-457: таблица аудит-журнала событий consent/learning-context обслуживалась тремя сервисами (gateway-mcp, learning-context-service, bot). Без `source_service` при инциденте невозможно было определить, какой сервис записал конкретную строку без чтения логов трёх отдельных систем.

## Тест обнаружения

«По строке лога можно определить, какой сервис её создал?» Нет → FM присутствует.

## Инвариант

Минимальная схема строки аудит-журнала: `{id, timestamp, event_type, actor_id, resource_id, source_service, payload_hash}`. Поле `source_service` — NOT NULL с enum или CHECK-constraint.

## Митигация

1. Добавить `source_service NOT NULL` в CREATE TABLE
2. Заполнять при каждой записи — имя сервиса или константа из кода
3. Добавить CHECK constraint или enum для исключения опечаток

## Связи

- DP.FM.091 (god-table cross-domain coupling) — смежный паттерн shared журнала без разделения ответственности
