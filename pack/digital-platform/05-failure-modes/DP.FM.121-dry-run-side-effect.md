---
id: DP.FM.121
name: "Dry-run side-effect — нарушение read-only обещания"
type: failure-mode
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-30
schema_version: 1
source: "peer-session 2026-05-30-22-wp7-opch6-sovereign-managed (_outcome.md, Critical fix C1)"
---

# DP.FM.121 — Dry-run side-effect (нарушение read-only обещания)

## Описание

`--dry-run` / `--plan-only` / `--check` обещают пользователю «ничего не изменится — можно проверить». Типичная ошибка реализации: вызов производит side-effect (запись в state/audit YAML/logs/БД-аналитика) при пути «вычислили план → залогировали → не отправили». При повторном dry-run state уже модифицирован, idempotency сломана.

## Симптом

- Повторный `--dry-run` возвращает другой результат (логи/audit/state различаются).
- Audit YAML растёт от dry-run прогонов так же, как от реальных.
- Partial-batch при ошибке оставляет audit в неконсистентном состоянии.
- Пользователь не отличит dry-run-audit от real-run-audit.

## Тест

«Если запустить `--dry-run` дважды подряд, отличается ли видимое состояние (audit/logs/state files)?» Да → нарушение invariant.

## Правило (митигация)

Для любого скрипта с `--dry-run` — обязателен **runtime invariant**:

```
diff $(state_before) $(state_after_dry_run) == empty
```

Реализация: smoke-test в CI/pre-commit проверяет, что dry-run не модифицирует listed state files. Все side-effect-функции получают `if not dry_run: ...` guard.

## Антипаттерн

«Dry-run печатает план + сохраняет audit как if-executed» — пользователь полагается на dry-run для безопасной проверки, но повторный запуск создаёт дубликаты строк.

## Применимо к

- Скриптам с side-effects: nudge senders, deployers, migrators, batch processors
- Любым `--dry-run` / `--plan-only` / `--check` режимам
- Promote/sync скриптам шаблона (template-sync, script-promote, hook-promote)