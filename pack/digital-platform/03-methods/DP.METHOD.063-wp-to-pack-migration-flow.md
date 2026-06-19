---
id: DP.METHOD.063
type: method
domain: PACK-digital-platform
status: draft
summary: "WP→Pack migration flow: WP-document = thinking workspace (mutable), Pack = canonical source of truth (stable). After crystallization — content migrates to Pack, WP moves to archive."
created: 2026-06-18
valid_from: 2026-06-18
version: v1.0
source: "session-transcript 2026-06-18; WP-327 migration to DP.ECON.001; WP-336 §3 migration to DP.ARCH.001"
related:
  see_also: [DP.ARCH.001, DP.D.001]
---

# DP.METHOD.063: WP→Pack Migration Flow

## Назначение

Операционный паттерн управления знанием через рабочие продукты (WP) и Pack-репозитории.
Решает проблему: WP-документы накапливают проектные решения, но со временем становятся вторым source-of-truth рядом с Pack — нарушение OwnerIntegrity.

## Поток

### 1. Черновик в WP

Рабочий документ `inbox/WP-NNN.md` в DS-репо — рабочее пространство мышления:
- Контент может быть незрелым, экспериментальным, внутренне противоречивым
- Обновляется по ходу работы над РП
- Не претендует на статус canonical source

### 2. Созревание решения

Контент кристаллизуется в устойчивые паттерны при условии:
- Решение прошло ArchGate или явное согласование
- Контент используется в 2+ местах или является reference для других РП
- Pack-репозиторий для этого домена определён

### 3. Миграция в Pack

Контент переносится в Pack как канонический источник:
- Определить целевой Pack и раздел (routing.md §1-4)
- Создать/обновить файл в Pack с правильным frontmatter
- WP-документ обновить: добавить ссылку `content_source: <pack-file-id>`

### 4. Архивация WP

После миграции WP-документ перемещается в архив (`0.OPS/0.99.Archive/`):
- WP становится historical record, не source of truth
- Ссылки на WP из других документов → перенаправить на Pack

## Тест OwnerIntegrity

«Есть ли сейчас два места, где хранится одно и то же знание?»
- В переходном состоянии (до архивации): допустимо (явно указать `content_source`)
- После архивации WP: нет дублирования

## Примеры применения

- WP-327-points-model → content migrated to `DP.ECON.001`, WP archived
- WP-336-platform-architecture §3 → content migrated to `DP.ARCH.001`, WP archived
