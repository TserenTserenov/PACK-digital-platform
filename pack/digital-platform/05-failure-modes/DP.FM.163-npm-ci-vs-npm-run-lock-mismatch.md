---
id: DP.FM.163
name: "Локально зелено, в CI красно: node_modules маскирует stale package-lock"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-15
source: "session-transcript 2026-06-14 + WP-359 + .claude/rules/distinctions.md (486c17b)"
related:
  references: [DP.FM.025]
tags: [build, ci, npm, package-manager, lock-file, environment-mismatch]
---

# DP.FM.163 — Локально зелено, в CI красно: node_modules маскирует stale package-lock

## Паттерн

Зависимость добавлена в `package.json`, но `package-lock.json` не пересобран. Локально `npm run build` / `npm run package` использует уже установленные `node_modules` → проходит. CI выполняет `npm ci` (строгая сверка lock ↔ package.json) → падает **на установке зависимостей**, ещё до запуска тестов.

**Сигнал:** «локально зелено, в облаке красно на установке зависимостей».

## Последствие

- Зелёный сигнал локально → коммит → красная сборка в CI
- Дефект воспроизводится **только** через `npm ci` (не `npm install`, не `npm run`)
- Усиливается при работе в команде: один разработчик правит `package.json`, не коммитит lock → у остальных продолжает работать (их `node_modules` совпадает с lock'ом), CI ловит первым

## Корневая причина

`npm install` / `npm run` толерантны: используют существующие `node_modules`, при необходимости лениво добавляют недостающее. `npm ci` строг: удаляет `node_modules`, ставит ровно по lock'у, падает при любом расхождении lock ↔ package.json.

## Правило

После любой правки зависимостей:

```bash
npm install          # обновить lock
git add package.json package-lock.json
git commit
npm ci               # верифицировать строгой установкой
```

Перед коммитом, который трогает зависимости, **верифицировать сборку через `npm ci`, а не через `npm run`**.

## Тест обнаружения

1. Изменён `package.json` без изменения `package-lock.json` в том же коммите? → ⚠️
2. Локальный `npm ci` падает с `Missing: ...` / `lock file mismatch`? → подтверждение
3. CI красный на шаге установки зависимостей, локально `npm run build` зелёный? → этот FM

## Профилактика

1. Pre-commit hook: при изменении `package.json` требовать одновременного изменения `package-lock.json`
2. CI-инвариант: всегда `npm ci`, никогда `npm install` в pipeline
3. Применимо к любому пакетному менеджеру с lock-файлом: `yarn install --frozen-lockfile`, `pnpm install --frozen-lockfile`, `bun install --frozen-lockfile`

## Связи

- **Родственный класс:** «Тесты зелёные ≠ миграция корректна» (01B-distinctions.md)
- **Близкий FM:** [DP.FM.025](DP.FM.025-monorepo-multisvc-f1-violation.md) — другая ось CI/CD дефектов
- **Источник:** WP-359, session-transcript 2026-06-14
