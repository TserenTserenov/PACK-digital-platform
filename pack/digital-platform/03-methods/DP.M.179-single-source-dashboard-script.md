---
id: DP.M.179
title: "Single-source dashboard-script для устранения stale-data"
type: method
domain: governance / observability
status: active
valid_from: 2026-05-26
sources:
  - peer-сессия 2026-05-26-18-ke-apply-captures-drift (report.md, тема 1 + резолюция)
  - commit c75d994b (ke-dashboard.sh)
related:
  - OwnerIntegrity (distinctions)
  - DP.M.178 quick-close триаж (потребитель свежих метрик)
trust: pilot-validated
---

# DP.M.179 — Single-source dashboard-script

## Проблема

Planning-документ (WeekPlan, status-page, dashboard.md) содержит числа, скопированные вручную из других мест:
- «На неделе: 12 активных РП, 8 pending captures, 3 critical»
- Числа были верны на момент записи; через час/день/неделю они протухают
- Документ даёт ложно-уверенное состояние («current»), но on-disk реальность другая

Пример: 2026-05-26 WeekPlan показывал «N pending captures» из ручной строки. Реально extraction-reports/ имел другое число (накопилось/применено за день).

## Метод

**Документ хранит ссылку на shell-скрипт, не сам показатель.**

Структура:
1. **Скрипт-верификатор** (`scripts/ke-dashboard.sh` и аналоги): обходит первичные файлы (`extraction-reports/*.md`, `inbox/WP-*.md`, git log) и считает текущее число.
2. **Документ:** одна строка вида `**Pending captures:** см. \`bash scripts/ke-dashboard.sh\`` вместо конкретного числа.
3. **Запуск:** ручной (агент / пилот) или cron — не требует MCP, не требует worker.

## Почему shell, а не MCP

MCP-сервер для этой задачи отклонён как overengineering:
- bash + glob + grep достаточно — числа считаются из файлов на диске
- любой агент с Bash tool (Claude Code, Kimi, CCR) может вызвать
- cron вызывает тривиально
- нет latency, нет авторизации, нет сетевых сбоев

## Применимость

- WeekPlan, DayPlan, status pages (метрики из файлов governance-репо)
- Capture queue / РП-реестр / hooks coverage / alert state
- Любой метрик-репорт, где SoT = файлы на диске (`*/extraction-reports/*.md`, git log, frontmatter)

## Антипаттерн

Вставлять цифру в Markdown «для удобства чтения» → через минуту она ложь. Лучше написать команду — пользователь увидит свежее значение или поймёт, что нужно запустить.
