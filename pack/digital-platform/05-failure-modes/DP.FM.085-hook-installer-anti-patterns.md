---
id: DP.FM.085
name: "Hook-installer anti-patterns: --no-verify, double-run, no-backup, no-diff-check"
type: failure-mode
pack: PACK-digital-platform
domain: tooling-hygiene
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-26
source: WP-7 DOE2 v2 (DS-my-strategy commit 06fbd789 — scripts/day-open-checks-runner.sh + install-hooks.sh)
---

# DP.FM.085 — Hook-installer anti-patterns: --no-verify, double-run, no-backup, no-diff-check

## Описание

Failure mode скриптов установки/обновления git-hooks (pre-commit, pre-push, post-merge). Четыре независимых анти-паттерна, накапливающихся в одном installer'е:

1. **`git commit --no-verify` внутри installer'а** — обходит другие hooks (secret-scan, lint, pre-commit-msg), которые мог настроить пользователь или другой installer.
2. **Двойной запуск hook'а** в одной транзакции — installer запускает свой же hook сразу после установки → false-green «работает!», хотя реальный сценарий (отдельная транзакция) не проверен.
3. **Перезапись существующего hook без backup** — если у пилота уже стоит кастомный hook (своя логика, чужой пакет), installer его молча сносит.
4. **Отсутствие `git diff --quiet`-чека** перед commit — installer коммитит даже когда изменений нет → шум «no-op»-коммитов в истории.

## Симптом

- В git log появляются коммиты installer'а без diff (пустые).
- Пользовательские кастомизации pre-commit/pre-push исчезают после `update.sh`.
- Secret-scan не срабатывает в коммитах installer'а (skipped через `--no-verify`).
- При прогоне CI «всё зелёное», но в реальной транзакции hook не срабатывает.

## Причина

«Удобство для разработчика installer'а» побеждает корректность:
- `--no-verify` ставится «чтобы быстрее закоммитить» → лазейка обхода других hooks.
- Двойной запуск — для проверки в той же сессии (но это false-green: hook вызывается явно, не как git-event).
- Backup пропускается «installer всегда правильнее, чем пользовательский hook» (неверное допущение).
- `git diff --quiet`-чек пропускается «коммит всё равно нужен» (нет — пустой коммит = шум).

## Профилактика

Hook-installer обязан выполнять последовательность:

1. **Backup существующего hook**: `cp .git/hooks/pre-commit .git/hooks/pre-commit.bak.<timestamp>` (если файл существует).
2. **Запись нового hook**: атомарно через tmp → mv.
3. **Проверка `git diff --quiet`**: если изменений нет (новый hook идентичен старому) → не коммитить, exit 0.
4. **Если изменения есть**: `git add <files>` → `git commit` **БЕЗ `--no-verify`** (пусть другие hooks работают).
5. **НЕ запускать установленный hook в той же сессии** — проверять в отдельной транзакции (smoke-test через `git commit --allow-empty -m "test"` в одноразовой ветке, не на main).

Согласуется с CLAUDE.md §2 п.6 «Hooks/Scripts Bypass Gate» (агентское правило не обходить hooks); этот FM покрывает противоположную сторону — installer hooks тоже обязан не обходить.

## Применимость

- `update.sh` обновляет hooks из FMT-шаблона.
- IWE skill-installer'ы, ставящие свои pre-commit/pre-push.
- Сторонние tool'ы (husky, pre-commit framework), если их wrapper переустанавливает.

## Тест обнаружения

`grep -rE "git commit.*--no-verify" scripts/install-hooks.sh` → 0 совпадений = ок, ≥1 = FM активен.
`grep -E "cp.*hooks/.*\.bak" scripts/install-hooks.sh` → 0 совпадений = FM #3 (нет backup).

## Связи

- AR.216 — pre-commit staged-only (соседний анти-паттерн hook scope)
- CLAUDE.md §2 п.6 — Hooks/Scripts Bypass Gate (агентская сторона)
- WP-7 DOE2 v2 (первый прецедент)
