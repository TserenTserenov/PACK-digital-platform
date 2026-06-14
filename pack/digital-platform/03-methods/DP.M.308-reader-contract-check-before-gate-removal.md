---
id: DP.M.308
type: method
title: "Проверка читателей перед удалением sender-side гейта"
aliases:
  - reader-contract-check-before-gate-removal
status: active
created: 2026-06-12
sources:
  - session: 2026-06-12
  - wp: WP-418
trust: medium
epistemic_stage: validated
---

# DP.M.308 — Проверка читателей перед удалением sender-side гейта

## Обещание

При миграции логики в централизованный сервис — предотвращает ослепление downstream-readers, которые читали данные удаляемого sender-side гейта (дедупликатор, cooldown, журнальный write).

## Алгоритм

1. **Grep readers по формату записи:**
   ```bash
   grep -rn "<journal_key>\|<event_type>\|<external_id>" --include="*.py" .
   ```
2. **Задокументировать каждого reader:** что читает, зачем, SLA
3. **Оценить безопасность удаления:**
   - Нет readers → безопасно удалять
   - Есть readers → сохранить контракт: semantic key переезжает в новый writer (drain/central service)
4. **Атомарный коммит:** новый writer + удаление старого гейта + smoke-test readers

## Когда применять

- Перед удалением любого sender-side state (дедупликатор, cooldown гейт, журнальная запись)
- При выносе логики в централизованный сервис (notification drain, central dedup)
- При рефакторинге event pipeline

## Тест

«Кто ещё читает то, что пишет этот гейт?» Если есть reader → контракт сохранить в новом writer'е.

## Связи

- `distinctions.md` → Дедуп-гейт отправителя ≠ приватная защёлка
- `distinctions.md` → Тесты зелёные ≠ миграция корректна (там readers таблиц, здесь readers журнала)
- DP.M.299 — rotation impact map (смежный)
- WP-418 Ф4
