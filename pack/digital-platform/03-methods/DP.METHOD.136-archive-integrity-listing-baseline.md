---
id: DP.METHOD.136
type: method
pack: PACK-digital-platform
domain: digital-platform / backup-verification
trust: draft
epistemic_stage: empirical
valid_from: 2026-07-09
source: "session-close 2026-07-06 (WP-7 BAK1-F2, peer-сессия BAK1 ход 24, CONSENSUS)"
schema_version: 1
related:
  see_also: [DP.FM.224]
---

# DP.METHOD.136 — Двухслойная верификация архива: листинг + baseline числа файлов

## Описание

Метод проверки целостности и полноты периодического архива (бэкапа) без полной распаковки. Два независимых слоя: структурная целостность (smoke) и полнота охвата (baseline). Дёшево, машинно-проверяемо, не требует дополнительного места.

## IPO

**Input:** путь к архиву (`.tar.gz`/`.tgz`), опционально baseline-файл с эталонным числом файлов

**Process:**

**Слой 1 — целостность (smoke):**
```bash
tar -tzf "$archive_path" > /dev/null 2>&1
```
Листинг без распаковки — одно последовательное чтение ловит обрыв или повреждение gzip. Если архив усечён или битый, `tar` вернёт ненулевой exit code.

**Слой 2 — полнота (baseline):**
```bash
# Установить/обновить эталон:
file_count=$(tar -tzf "$archive_path" | wc -l)
echo "{\"baseline\": $file_count, \"set_at\": \"$(date -I)\"}" > "$baseline_file"

# Проверить:
current=$(tar -tzf "$archive_path" | wc -l)
baseline=$(jq .baseline "$baseline_file")
ratio=$(echo "scale=2; $current / $baseline" | bc)
[ "$(echo "$ratio >= 0.80" | bc)" -eq 1 ] || echo "WARN: coverage dropped to ${ratio}"
```

Идемпотентность `--reset-baseline`: повторный вызов без изменений архива — INFO, не ошибка. Baseline хранит дату установки для контроля актуальности эталона.

**Output:** статус `OK` / `FAIL` (битый) / `WARN` (деградация полноты) / `SKIP` (заглушка, см. DP.FM.224)

## Когда применять

- Периодические архивы: ночные бэкапы, дампы данных, snapshot-артефакты
- Нет ресурсов или места для полного restore
- Нужен машинно-проверяемый результат в CI/cron (не ручная проверка)

## Антипаттерн

`[ -f "$archive_path" ] && echo "OK"` — существование файла ≠ целостность содержимого. При smart-sync (DP.FM.224) файл существует с 0 KB — guard по размеру обязателен перед `tar`.

## Границы применимости

Листинг `tar -tzf` ловит обрыв gzip и повреждение заголовков, но не обнаруживает тихую коррупцию данных внутри блоков без повреждения структуры. Для критичных данных (БД дампы) — дополнительно `--compare` или контрольная сумма.
