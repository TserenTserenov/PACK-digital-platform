---
id: DP.METHOD.055
name: Метод безопасного автоматического git push (push-invariant)
type: method
status: active
valid_from: 2026-06-11
summary: "Тройное условие перед автоматическим push в CI/CD или cron: clean tree AND ahead>0 AND behind==0. При behind>0 (diverged) — молча пропустить, сигнализирует pull-alert компонент."
related:
  see_also:
    - DP.D.140   # Наблюдатель ≠ Сервис доставки
  realized_by:
    - iwe-push-ahead service
created: 2026-06-11
updated: 2026-06-11
tags: [git, ci-cd, automation, push, invariant, safe-push]
source: "git diff iwe-server-config (484821f, pushAheadScript), session 2026-06-11"
schema_version: 1
---

# DP.METHOD.055 — Метод безопасного автоматического git push

> **see DP.D.140** (Наблюдатель-сторож ≠ Сервис доставки)

## §0 Назначение

Метод задаёт минимальный инвариант для автоматического push в неинтерактивном окружении (cron, CI/CD, серверный агент) без риска merge-конфликтов или потери контента.

## §1 Тройное условие (push-invariant)

Автоматический push выполняется строго при одновременном выполнении всех трёх условий:

1. **clean tree** — нет незакоммиченных изменений (`git status --porcelain` пуст)
2. **ahead > 0** — есть коммиты для доставки (`git rev-list @{u}..HEAD | wc -l > 0`)
3. **behind == 0** — нет входящих изменений, merge не потребуется (`git rev-list HEAD..@{u} | wc -l == 0`)

При behind > 0 (diverged state) — молча пропустить: за сигнализацию отвечает отдельный pull-alert компонент (см. DP.D.140).

## §2 Почему три условия

- **clean tree** — грязное дерево означает незафиксированный прогресс, push прервёт состояние на полпути
- **ahead > 0** — пустой push — холостое действие, зашумляет logs
- **behind == 0** — push при behind > 0 потребует merge/rebase: в автоматическом режиме это мутация истории без надзора

## §3 Применение

```bash
ahead=$(git rev-list @{u}..HEAD 2>/dev/null | wc -l | tr -d ' ')
behind=$(git rev-list HEAD..@{u} 2>/dev/null | wc -l | tr -d ' ')
status=$(git status --porcelain 2>/dev/null)

if [ -z "$status" ] && [ "$ahead" -gt 0 ] && [ "$behind" -eq 0 ]; then
  git push
fi
```

**Применимо:** любой CI/CD шаг «автоматический push», cron-задачи на серверах, headless git-агенты.
