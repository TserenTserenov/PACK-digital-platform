---
id: DP.D.108
name: "Type-string runtime drift ≠ File-replace terminology drift"
type: distinction
status: active
valid_from: 2026-05-30
summary: "Два класса drift'а вокабуляра. Runtime: writer и resolver обмениваются через string literal без shared enum — новые значения silently попадают в else-ветку. File-replace: переименование термина в файлах через sed — пропущенные места остаются с old name."
related:
  see_also: []
wp: WP-7 OPCH cold-review (Critical C2)
sources:
  - DS-my-strategy/inbox/captures.md:6644
  - commit 9396e057
---

# DP.D.108: Type-string runtime drift ≠ File-replace terminology drift

> Два класса drift'а вокабуляра между компонентами. Связаны механизмом «разное знание о наборе допустимых значений», но требуют разных контрмер.

## Runtime drift (type-string coupling)

**Что:** Writer и resolver обмениваются через literal string без shared enum / constant / schema. Writer эволюционирует ('managed' → 'managed' + 'personal' → + 'sovereign'), resolver знает только подмножество. Новые значения silently попадают в else-ветку с непредусмотренным поведением.

**Тест распознавания:** «Обмен между компонентами через string literal в обе стороны без shared constant?» Да → drift unavoidable, вопрос времени.

**Невидимость:** тесты writer проходят (он пишет что говорит спека), тесты resolver проходят (он различает managed/не-managed), интеграционный путь — silent fall-through.

**Контрмеры:**
- (а) shared enum / `Literal` type в общем модуле, импортируемом writer'ом и resolver'ом;
- (б) resolver exhaustively matches с `else: raise ValueError(unknown_type)` (fail-fast);
- (в) integration smoke с каждым valid type из enum.

**Прецедент:** `iwe-guide-web/main.py:1458` `/setup` route писал `repo_type='personal'`, а `resolve_repo` проверял только `=='managed'` → 'personal' проваливался в sovereign-ветку с пустым owner. WP-7 OPCH cold-review Critical C2 (commit 9396e057, 30 мая 2026).

## File-replace drift (terminology rename)

**Что:** Переименование термина в файлах через sed / IDE-rename. Один-два прохода verify→fix оставляют inconsistency — старое имя в пропущенных местах.

**Контрмеры:** lessons_terminology_verify_loop.md — 2-3 прохода verify→fix, не предполагать чистоту после первого.

**Прецедент:** 22 мая 2026 (memory/lessons_terminology_verify_loop.md).

## Граница

Runtime drift — про contract между двумя живыми процессами через string-based dispatching (один пишет, другой читает в реальном времени). File-replace drift — про синхронность переименования в статических артефактах.

| Ось | Runtime drift | File-replace drift |
|-----|---------------|-------------------|
| Где живёт coupling | В runtime-обмене (API payload, queue message, config field) | В файлах исходников |
| Как обнаруживается | Production-баг или integration test | grep после rename |
| Защита | Shared enum + exhaustive match | Многопроходный verify |

## Применимость

Runtime drift: API contracts, event payloads, config schemas, queue message types, any cross-component string-based dispatching.

File-replace drift: переименование domain terms, переименование функций/классов, миграции названий в Pack.

## Связи

- **Источник прецедента (runtime):** WP-7 OPCH cold-review Critical C2.
- **Источник прецедента (file-replace):** `memory/lessons_terminology_verify_loop.md`.
- **Соседнее различение:** DP.D.080 (контрольная vs операционная роль) — другая ось разделения компонентов.
