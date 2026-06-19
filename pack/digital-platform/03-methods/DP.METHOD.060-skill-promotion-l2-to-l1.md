---
id: DP.METHOD.060
title: "Skill promotion: L2 (авторское) → L1 (платформенное)"
type: method
pack: DP
tags: [skill, promotion, l2, l1, fmt, platform, template-sync, skill-promote]
status: draft
valid_from: 2026-06-16
schema_version: 1
---

# DP.METHOD.060 — Skill promotion: L2 (авторское) → L1 (платформенное)

## Назначение

Процедура переноса скилла из авторского IWE (L2, `/Users/tserentserenov/IWE/.claude/skills/<skill-name>/`) в шаблон платформы (L1, `FMT-exocortex-template/.claude/skills/<skill-name>/`). Делает скилл доступным для всех новых пользователей IWE через `update.sh`.

## Чеклист промоции

1. **Удалить авторские константы** из SKILL.md:
   - Личные пути (`~/IWE/`, `tserentserenov`) → `{{IWE_ROOT}}`, `{{HOME_DIR}}`
   - Репо-имена → `{{IWE_GOVERNANCE_REPO}}`
   - Личные email/id → убрать или `{{AUTHOR_EMAIL}}`

2. **Прогнать валидатор синтаксиса:**
   ```bash
   bash $IWE_SCRIPTS/validate-fmt-scripts.sh $IWE_SCRIPTS/
   ```

3. **Запустить skill-promote:**
   ```bash
   bash $IWE_SCRIPTS/skill-promote.sh <skill-name>/ [--dry-run]
   ```

4. **Проверить copy в FMT:**
   ```bash
   diff ~/IWE/.claude/skills/<skill-name>/SKILL.md \
        ~/IWE/FMT-exocortex-template/.claude/skills/<skill-name>/SKILL.md
   ```

5. **Коммит в FMT:**
   ```bash
   cd ~/IWE/FMT-exocortex-template && git add .claude/skills/<skill-name>/ && \
   git commit -m "feat: promote skill <name> from L2 to L1"
   ```

## Тест применимости

«Скилл запускается у нового пользователя после `update.sh` без дополнительных настроек?» Да → промоция выполнена корректно.

## Когда НЕ применять

Скилл содержит авторскую логику (личные репо, личные workflow), которую нельзя обобщить → оставить в L2, не промотировать.

## Источник

session-transcript 2026-06-16; WP-422 Ф6 (skill-promotion review)
