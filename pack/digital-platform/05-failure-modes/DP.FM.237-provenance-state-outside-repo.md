---
id: DP.FM.237
name: "Provenance-стейт стража вне репо: решение работает на одной машине, невидимо CI"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-26
source: "peer-session 2026-06-26-26-wp436-claude-writer, turn 2; WP-436"
related:
  references: [DP.FM.009]
  see_also: ["DP.D.222 (non-blocking audit ≠ protection layer)", "distinctions.md: Local Gateway ≠ Cloud Gateway"]
tags: [provenance, enforcement, ci, environment-parity, security-gate]
---

# DP.FM.237 — Provenance-стейт стража вне репо: «работает на моей машине»

## Паттерн

Guard хранит provenance-поле (`enabled_at`, state) в локальном файле вне репо (`~/.iwe/...`). Локально работает. В CI, cron, remote — файл отсутствует → guard ведёт себя непредсказуемо или блокирует всё.

## Пример

```
# gate-run --enable handler=foo
# Пишет: ~/.iwe/gate-handler-state.jsonl {"handler": "foo", "enabled_at": "2026-06-26T..."}

# D4-guard в CI:
#   open("~/.iwe/gate-handler-state.jsonl")  → FileNotFoundError
#   → fail-closed: блокирует ВСЕ обработчики
#   → fail-open: разрешает ВСЕ (уничтожает enforcement)
```

## Механизм

1. `gate-run --enable` выполняется на ноутбуке разработчика → запись в `~/.iwe/`
2. CI/cron запускается на другом хосте/контейнере — `~/.iwe/` там пуст
3. D4-guard читает стейт → не находит → поведение зависит от реализации fail-mode
4. Enforcement-инвариант нарушен: локально «enabled», в CI «blocked» или «open»

## Почему опасен

- Невидим: разработчик видит локальный PASS — уверен, что настроено правильно
- Непредсказуем: поведение guard в CI зависит от fail-mode реализации
- Не воспроизводим: коллега на другой машине получит иное поведение
- Обнаруживается только при первом CI-прогоне или при code review

## Лечение

Стейт, на котором страж принимает решение, обязан быть виден ВСЕМ экземплярам стража:

1. **Защищённый файл в репо** (append-only + подписанные коммиты + CODEOWNERS)
2. **Центральное хранилище** (DB/API), доступное и локально и в CI по одной конфигурации
3. **Часть deployment-артефакта** (env var, secrets manager)

Append-only — хорошее свойство, но недостаточное без cross-environment visibility.

## Связи

- Усиливает: Local Gateway ≠ Cloud Gateway
- Смежный: DP.FM.009 (hardcoded script path — та же проблема среды, другой домен)
- Distinction: DP.D.222 (non-blocking audit = advisory)
- Contermeasure: DP.METHOD.154 (bypass-class taxonomy — класс field-spoof)
