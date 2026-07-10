---
id: DP.METHOD.133
name: GitHub Actions workflow permissions declaration
name_ru: Декларация permissions в GitHub Actions workflow
type: method
status: established
summary: "GitHub Actions workflow с `permissions: contents: write` не требует отдельной настройки прав на уровне репо. GITHUB_TOKEN получает права из декларации в workflow. Применимо ко всем workflow с push/commit/release операциями."
created: 2026-07-09
trust:
  F: 2
  G: domain
  R: 0.95
epistemic_stage: established
related:
  complements: [DP.M.104, DP.METHOD.132]
tags: [github-actions, permissions, security, workflow, github-token, write-access]
wp: WP-415
sources:
  - session-transcript 2026-07-06
  - git diff DS-my-strategy commit deafe21ed
---

# DP.METHOD.133 — Декларация permissions в GitHub Actions workflow

## Контекст

При добавлении GitHub Actions workflow с write-операциями (push, commit, gh pr create) возникает вопрос: нужно ли настраивать repo-level права на запись отдельно?

## Суть

**Нет.** Достаточно объявить права в самом workflow:

```yaml
permissions:
  contents: write
```

`GITHUB_TOKEN` получает права из этой декларации. Отдельная настройка repo-level write permissions — избыточна.

## Инварианты

- Декларация `permissions:` сужает scope токена до минимально необходимого (принцип наименьших привилегий)
- Без декларации workflow получает дефолтные права (read-only для большинства операций)
- Применимо ко всем workflows с `git push`, `git commit`, `gh pr create`, `gh release create`

## Список действий для пилота

При добавлении нового workflow с write-операциями:
1. Добавить `permissions: contents: write` в workflow YAML
2. Положить нужные внешние ключи как repo secrets (напр. `ANTHROPIC_API_KEY`)
3. Настройка прав репо вручную — **не требуется**
