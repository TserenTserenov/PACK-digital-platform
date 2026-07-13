---
name: mtime как критерий scope-gate недостаточен при headless-операциях
id: DP.FM.272
domains: [agent-session, scope-gate, headless]
tags: [session-scope, mtime, note-file, headless-ops]
severity: medium
---

# DP.FM.272 — mtime как критерий scope-gate при headless-операциях

## Суть

Session-guard использует mtime (время модификации) файла для определения принадлежности файла текущей сессии. При наличии headless-операций (Day Open, cron-задачи) агент создаёт новые файлы вне интерактивного контекста — их mtime совпадает с временем сессии, но эти файлы не входили в рабочий контекст. Итог: guard блокирует легитимные коммиты headless-операций, считая их файлы «чужими».

## Инцидент

WP-7 SGFIX4 (commit 946e4a3): headless Day Open создавал новые файлы — scope-gate их отвергал как не принадлежащие сессии.

## Fix

Для новых файлов — явный session note-file (семафор), который агент создаёт при открытии сессии и содержит список санкционированных файлов. Guard проверяет наличие файла в note-file, не mtime.

## Паттерн

**Тест:** «Агент создаёт файлы без интерактивного открытия сессии?» Да → mtime-gate недостаточен, нужен note-file семафор.

**Смежно:** [DP.FM.260] (path resolution vs logic error в checks).

**Источник:** session-close 2026-07-08 (commit 946e4a3, WP-7 SGFIX4).
