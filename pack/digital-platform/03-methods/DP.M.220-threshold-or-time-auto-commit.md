---
id: DP.M.220
name: "Threshold-or-time авто-коммит с daily squash"
type: method
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "git diff DS-agent-workspace f70e466, peer-session 2026-05-28-02-bot-problems-peer"
---

# DP.M.220 Threshold-or-time авто-коммит с daily squash

## Описание

Паттерн для агентских workspace'ов с накопительной записью: commit происходит только при выполнении двойного условия «>N файлов ИЛИ >M минут с последнего коммита»; daily squash (05:00) сворачивает все авто-коммиты дня в один. Балансирует два противоречащих требования: своевременность фиксации (малое окно dirty state) и чистота git log (без шума).

## IPO

**Вход:** агентский workspace с непрерывной записью файлов, threshold N (кол-во файлов), threshold M (минуты), cron squash time
**Процесс:**
1. При каждом тике: если `changed_files > N` ИЛИ `minutes_since_last_commit > M` → commit
2. При 05:00: `git rebase -i --autosquash HEAD~{auto_commit_count}` → squash всех авто-коммитов в один
**Выход:** dirty state window ≤ M минут; git log содержит 1 запись за день (не N * ticks)

## Параметры

| Параметр | Типовое значение | Обоснование |
|----------|-----------------|-------------|
| N (файлы) | 10 | Коммит при значимом объёме изменений |
| M (минуты) | 30 | Не накапливать dirty state дольше получаса |
| Squash time | 05:00 | До начала Day Open, нет конфликта с рабочей записью |

## Когда применять

- Агентский workspace с частой мелкой записью (journal, captures, logs)
- Ретроспективный git blame не нужен для авто-коммитов (если нужен точный trace → отключить squash)
- Несколько активных агентов: squash уменьшает конфликты при merge

## Тест

«Нужен ли точный git blame по каждому авто-коммиту?» Нет → squash допустим. «Допустимо ли dirty state до M минут?» Да → threshold-or-time достаточно.

## Связи

- **WP-330** — DS-agent-workspace (эмпирическое применение: fix threshold trigger)
- **peer-session 2026-05-28-02** — обсуждение паттерна (bot-problems-peer)
