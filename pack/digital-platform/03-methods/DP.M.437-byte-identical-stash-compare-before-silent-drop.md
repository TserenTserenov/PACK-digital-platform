---
id: DP.M.437
name: "Побайтовое сравнение застешенной правки с новым HEAD — тест на право тихого удаления"
type: method
pack: PACK-digital-platform
domain: digital-platform / git-auto-repair
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-09-09
source: "git commit 91a964049c в DS-my-strategy (scripts/canon-reconcile.sh), WP-530 Ф32"
see_also: [DP.M.372, DP.FM.390]
schema_version: 1
---

# DP.M.437 — Побайтовое сравнение застешенной правки с новым HEAD как тест на право тихого удаления

## Проблема
Существующий авто-починщик чистого-но-устаревшего checkout намеренно не трогал грязное дерево — незакоммиченная правка живого редактора не держит лок, и слепой reset/fast-forward мог бы её уничтожить. Нужен безопасный способ авто-починки И грязного отставшего checkout, не рискуя чужой непубликованной работой.

## IPO
- **Вход:** checkout одновременно грязный (unstaged/staged правки блокируют fast-forward) и отставший от origin
- **Процесс:** `git stash` только путей, блокирующих fast-forward → fast-forward → побайтовое сравнение содержимого каждого застешенного пути с тем, что теперь на новом HEAD
- **Выход:** byte-identical → правка уже опубликована где-то ранее, стеш тихо выбрасывается; отличие → потенциально непубликованная правка, остаётся в stash и явно называется в выводе для ручного разбора — никогда не отбрасывается автоматически

## Паттерн
```
stash exactly the paths blocking fast-forward
fast-forward
for path in stashed_paths:
    if content(path, stash) == content(path, new HEAD):
        drop silently   # уже неактуально, опубликовано ранее
    else:
        keep in stash, name it in output   # потенциально реальная правка → ручной разбор
```

## Применимо
Автоматическая починка checkout, одновременно грязного и отставшего, когда нельзя терять непубликованную работу живого редактора, но и нежелательно блокировать авто-починку каждым уже-неактуальным дифом.

## Связи
- DP.M.372 (flock granularity) — смежный инструмент той же серии (`canon-reconcile.sh`/`canon-refresh.sh`), другой аспект безопасной автоматизации git.
- DP.FM.390 (blind-stash-pop-orphaned-autostash) — родственный failure mode: там слепой `stash pop` без построчной проверки теряет чужие удаления; здесь — явный побайтовый тест ДО отбрасывания, профилактика той же категории ошибки.
- Источник: WP-530 Ф32, canon-refresh.sh (WP-484 AF)
