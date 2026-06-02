---
id: DP.FM.108
name: Owner-резолвер с пустым default из единственного источника (adopted-sovereign trap)
type: failure-mode
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-30
schema_version: 1
sources:
  - DS-my-strategy/inbox/captures.md:6654
  - WP-7 OPCH cold-review (Critical C3)
  - commit 0badab6
---

# DP.FM.108 — Owner-резолвер с пустым default из единственного источника

## Описание

Ownership / tenant / namespace resolution из **единственного primary-источника** (yaml-конфиг, lookup table, registry DB) с тихим empty-default'ом для отсутствующих ключей. Для любой сущности, не попавшей в primary, owner становится пустой строкой; downstream-консьюмер (renderer, authz-guard, audit log) падает 404 / permission-denied / null-pointer.

## Симптом

Адопшен новой сущности (пилота, репо, тенанта) видимо успешен — запись создана, audit log позитивный. Но первый клик пользователя по entity-странице даёт 404 или 403. Логи показывают `owner=""` в момент resolve.

## Тест

«Есть ли в коде паттерн `owner = X.get(id, "")` или `owner = X[id] if id in X else ""` или `owner = X.get(id) or default_empty`?» Да → создаёт trap для любого ID за пределами X.

## Прецедент (WP-7 OPCH C3, 30 мая 2026)

`create-managed-repo.py:439` писал `org_name=github_username` для sovereign-варианта, но `resolve_repo` игнорировал `org_name` для sovereign и брал owner ТОЛЬКО из yaml-конфига пилотов. Для adopted-пилотов, не вошедших в yaml → owner="" → render 404 на гайдовой странице. Fix commit `0badab6`: для sovereign fallback `yaml_owner → org_name → ""` с explicit-empty-fail.

## Правило (контрмера)

Fallback-цепочка для ownership-резолвера обязана:
1. Enumerate **≥2 источника** в порядке приоритета.
2. Заканчиваться **explicit-fail на пустоте**, не silent empty default.

**Шаблон:**

```python
owner = (
    yaml_config.get(entity_id)
    or derived_from_context(entity_id)
    or raise OwnerResolutionError(entity_id)
)
```

## Граница (не путать)

| Антипаттерн | Механизм |
|------------|----------|
| **DP.FM.108 (этот)** | Один источник, отсутствие ID → empty default → trap |
| Mass-migration overrides choice (lessons_mass_migration_overrides_choice.md) | Один источник перезаписывает пользовательский выбор |

Зеркальные антипаттерны: один — про дефицит источника, другой — про избыток (агрессивную перезапись).

## Применимость

- ACL resolvers (кто владеет ресурсом?);
- tenant routing (в какой тенант идёт запрос?);
- multi-tenant config lookups;
- любая сущность, которая может «быть в системе, но не быть зарегистрированной» в primary index.

## Связи

- **Источник:** WP-7 OPCH cold-review (commit 0badab6).
- **Зеркальный антипаттерн:** `memory/lessons_mass_migration_overrides_choice.md`.
- **Соседний FM:** DP.FM.104 (missing-reverse-identity-lookup) — другая ось lookup-проблем (нет обратного направления).
