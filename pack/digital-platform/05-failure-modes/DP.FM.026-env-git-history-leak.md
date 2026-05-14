---
id: DP.FM.026
name: .env в git history — утечка secrets + обязательные шаги ликвидации
type: failure-mode
domain: DP
status: active
valid_from: 2026-05-12
source: WP-307 Ф3, 12-factor F3
---

# DP.FM.026 — .env в git history: нарушение 12-factor F3

## Паттерн

Файл `.env` с реальными секретами случайно добавлен в `git add .` и закоммичен. Даже после `git rm .env` файл остаётся в истории — доступен через `git log --all --full-history -- .env`.

## Сигнал аудита

```bash
# Проверить, был ли .env в истории
git log --all --full-history -- .env
# Проверить содержимое по всей истории
git grep -I 'API_KEY\|SECRET\|TOKEN' $(git rev-list --all)
```

## Обязательные шаги при обнаружении

### Шаг 1 — Ротация (немедленно)

Ротировать ВСЕ скомпрометированные secrets, даже если репо приватный. История могла быть склонирована коллаборантами.

### Шаг 2 — Очистка истории

```bash
# Вариант A: BFG Repo-Cleaner (быстрее)
bfg --delete-files .env
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push --force-with-lease

# Вариант B: git filter-repo
git filter-repo --path .env --invert-paths
```

### Шаг 3 — Защита от повторения

`.gitignore` должен покрывать ВСЕ варианты:
```
.env
.env.*
**/.env
**/env/
```

`.env.example` (без secrets) — обязательный артефакт.

## Паттерны защиты (превентивные)

- `pre-commit` hook: `git-secrets` или `detect-secrets`
- CI-сканер: Gitleaks, truffleHog
- Template `.gitignore` для всех новых репо

## Связи

- 12-factor F3: Config (secrets вне кода)
- Контекст: WP-307 Ф3, M6 google-drive-mcp (.env в history)
- Смежно: DP.FM.024, DP.FM.025 (12-factor violations)
