---
id: DP.D.137
name: "exocortex/CLAUDE.md slot (workspace-root backup) ≠ governance CLAUDE.md"
name_ru: "Слот exocortex/CLAUDE.md (бэкап workspace-root) ≠ governance CLAUDE.md"
type: distinction
domain: digital-platform
pack_refs: [DP.FM.157]
status: active
valid_from: 2026-06-13
schema_version: 1
source: "git diff FMT-exocortex-template + peer-session 2026-06-13-17, commit 158b31b, PR #172"
---

# DP.D.137 exocortex/CLAUDE.md slot ≠ governance CLAUDE.md

| Аспект | exocortex/CLAUDE.md (слот) | governance CLAUDE.md |
|--------|---------------------------|----------------------|
| **Семантика** | Бэкап workspace-root инструкций агента | Инструкции для governance-репо |
| **Восстанавливается в** | `$WORKSPACE_DIR/CLAUDE.md` через iwe-restore | Остаётся в git governance-репо |
| **Версионирование** | Exocortex-бэкап (snapshot) | Git-история |
| **Доступность из облака** | Нет — только локально через day-close.sh | Да — CI/CD имеет доступ к governance-репо |
| **Критичность потери** | Агент сессии работает без инструкций | Восстанавливается git checkout |

## Инвариант

`exocortex/CLAUDE.md` = backup of workspace-root `CLAUDE.md`. `restore-from-exocortex.sh` разворачивает именно этот слот в `$WORKSPACE_DIR/CLAUDE.md`. Governance CLAUDE.md имеет независимый lifecycle и отдельное хранение.

## Применение

Важно при:
1. Восстановлении среды на новой машине (iwe-restore) — разворачивается workspace-root
2. Написании cloud-backup скриптов — этот слот нельзя заполнять из облака (нет доступа к workspace-root)
3. Добавлении нового CLAUDE.md-уровня в топологию — решить явно, что бэкапировать в какой слот

## Тест

«Что будет восстановлено через iwe-restore?» exocortex/CLAUDE.md → `~/IWE/CLAUDE.md` (workspace-root). Нарушение незаметно: файл «есть», содержимое «похоже» на правильное, ошибка проявляется в тонком поведении агента при работе.

## Связи

- DP.FM.157 (cloud backup overwrites wrong CLAUDE.md layer) — failure mode нарушения этой семантики
- iwe-restore.sh — consumer данного слота
