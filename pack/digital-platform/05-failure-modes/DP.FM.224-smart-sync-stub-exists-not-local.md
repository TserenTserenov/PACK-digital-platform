---
id: DP.FM.224
title: "Smart-sync cloud stub: файл существует, содержимое не материализовано локально"
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform / backup-verification
trust: draft
epistemic_stage: observed
valid_from: 2026-07-09
source: "session-close 2026-07-06 (WP-7 BAK1-F2, iwe-backup-check.sh)"
related:
  see_also: [DP.FM.078]
tags: [icloud, smart-sync, backup, verification, cloud-stub, onedrive, dropbox]
---

# DP.FM.224 — Smart-sync cloud stub: файл существует, содержимое не материализовано локально

## Описание

При включённом «Optimize Mac Storage» (iCloud), OneDrive Files On-Demand или Dropbox Smart Sync файл присутствует по пути в файловой системе, но локально занимает 0 KB — это заглушка, реальное содержимое в облаке. Проверка существования файла (`-f`, `os.path.exists`) проходит успешно, тогда как содержательные операции (smoke-тест целостности, подсчёт файлов, hash) дают ложный результат: архив выглядит «пустым» или «битым».

## Механизм

Smart-sync создаёт заглушку (stub) в локальной FS с сохранением метаданных. Открытие файла инициирует загрузку из облака — но если запрос происходит в скрипте без явного ожидания материализации, размер файла остаётся 0 KB. Верификатор видит «существующий» архив и фиксирует FAIL по содержимому — не потому что архив повреждён, а потому что он не загружен.

## Признаки

- Проверка `[ -f ... ]` или `os.path.exists()` возвращает True
- Размер файла (`du -k`, `wc -c`, `stat`) — 0 или несколько KB (метаданные)
- `tar -tzf` / hash-верификация падает с ошибкой gzip или возвращает 0 файлов
- Другие файлы в той же директории могут быть материализованы нормально

## Митигация

Перед содержательными проверками — guard по локальному размеру:

```bash
local_size=$(du -k "$archive_path" | cut -f1)
if [ "$local_size" -lt 10 ]; then
    echo "SKIP: archive not materialized locally (${local_size}KB) — cloud stub"
    exit 0
fi
```

Статус при пропуске: `«архив не материализован»`, не `FAIL` — иначе ложная тревога будит дежурного.

## Границы применимости

Применимо к любому smart-sync хранилищу: iCloud Drive (Optimize Mac Storage), OneDrive Files On-Demand, Dropbox Smart Sync. Не применимо к локальным дискам, NAS с полной синхронизацией, S3-маунтам (там свои паттерны).
