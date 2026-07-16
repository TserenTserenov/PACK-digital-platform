---
id: DP.FM.288
name: "Shell-гейт: NUL-разделитель не сгенерирован → цикл выполняет 0 итераций → exit 0"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-14
source: "session-close 2026-07-09; DS-my-strategy commits 6effd298a, d31c83d (WP-5 day-open-checks-runner.sh)"
related:
  see_also:
    - "DP.FM.038: validator-silent-pass (пустой вход → зелёный)"
    - "DP.FM.192: subshell-redirect-silences-exit-code (другой механизм поглощения exit)"
tags: [shell, bash, nul, awk, portability, false-green, gate, loop, macos]
---

# DP.FM.288 — Shell-гейт: NUL-разделитель не сгенерирован → 0 итераций → exit 0

## Паттерн

Shell-скрипт-гейт разбивает входные данные на блоки через NUL-байт и обходит их циклом
`while IFS= read -r -d ''`. Если NUL не генерируется корректно, цикл выполняет 0 итераций
и скрипт завершается с exit 0, сообщая об успехе без единой проверки.

**Конкретный кейс (WP-5 day-open-checks-runner.sh):** `awk '\x00'` на macOS/launchd
не разворачивает `\x00` в реальный NUL-байт (в отличие от GNU awk).
Скрипт с 17 блокирующими проверками был нерабочим с первого дня — несколько месяцев.

## Пример

```bash
# СЛОМАНО — macOS awk не генерирует NUL-байт:
blocks=$(cat plan.md | awk '/^##/{if(b) print b"\x00"; b=""} {b=b$0"\n"} END{print b}')
BLOCKS_RUN=0
while IFS= read -r -d '' block; do
  check_block "$block"
  ((BLOCKS_RUN++))
done <<< "$blocks"
echo "All $BLOCKS_RUN checks passed"  # ← "All 0 checks passed" всегда

# ПРАВИЛЬНО — printf для NUL + счётчик:
blocks=$(cat plan.md | awk '/^##/{if(b) printf "%s%c", b, 0; b=""} {b=b$0"\n"} END{printf "%s%c", b, 0}')
BLOCKS_RUN=0
BLOCKS_TOTAL=$(grep -c '^##' plan.md || echo 0)
while IFS= read -r -d '' block; do
  check_block "$block"
  ((BLOCKS_RUN++))
done <<< "$blocks"

if [ "$BLOCKS_RUN" -eq 0 ]; then
  echo "ERROR: гейт выполнил 0 блоков из $BLOCKS_TOTAL" >&2; exit 1
fi
echo "Passed: $BLOCKS_RUN/$BLOCKS_TOTAL"
```

## Диагностика

**Тест:** «Скрипт-гейт проверяет, что BLOCKS_RUN > 0 перед сообщением об успехе?»
Нет → уязвим к silent pass при любой ошибке парсинга разделителя.

**Сигнал:** Гейт всегда завершается с exit 0 независимо от содержимого входных данных.
`echo "BLOCKS_RUN=$BLOCKS_RUN"` после цикла — первый шаг диагностики.

## Защита

1. **NUL-генерация через `printf "%c" 0`** вместо `awk '\x00'` (кросс-платформенно)
2. **Счётчик BLOCKS_RUN** — инкрементировать в каждой итерации цикла
3. **Guard:** `if [ "$BLOCKS_RUN" -eq 0 ]; then echo "ERROR: 0 blocks" >&2; exit 1; fi`
4. **Тест readiness гейта:** вставить заведомо ломаный артефакт → проверить, что гейт заблокировал

## Применимость

Любой shell-скрипт с `while IFS= read -r -d ''` для парсинга NUL-разделённых блоков.
Особенно актуально на macOS/BSD (launchd), где awk не разворачивает `\x00`.

## Связи

- **DP.FM.038** (Validator silent pass) — мета-категория «zero-execution false-green»; FM.038 = пустой вход, этот FM = нулевые итерации цикла из-за парсинга разделителя
- **DP.FM.192** (subshell-redirect-silences-exit-code) — другой механизм тихого поглощения exit code
