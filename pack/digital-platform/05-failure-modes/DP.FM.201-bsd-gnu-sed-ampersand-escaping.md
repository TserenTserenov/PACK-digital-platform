---
id: DP.FM.201
title: "BSD sed vs GNU sed: capture group вместо `&` для кросс-платформенной безопасности"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / scripting
epistemic_stage: confirmed
valid_from: 2026-07-04
source: "session-close 2026-07-04 (FMT issue #220)"
related:
  see_also: [DP.FM.033]
---

# DP.FM.201 — BSD sed vs GNU sed: `&` в строке замены — edge-case divergence

## Описание

BSD sed (macOS) и GNU sed разделяют семантику `&` как «весь matched pattern» в строке замены, но поведение расходится в edge-cases при использовании внутри сложных замен с флагом `-E`. Скрипт, тестированный на GNU/Linux (CI), тихо ломается на macOS.

## Пример

FMT issue #220: drift-detector скрипт использовал `& suffix` в BSD sed replacement string. Тест на macOS дал неверный результат; скрипт проходил CI на GNU Linux без ошибок. Fix: замена `&` на capture group `\1`.

## Тест обнаружения

```bash
echo "test" | sed -E 's/(test)/& suffix/'
```
Ожидаемый вывод: `test suffix`. На macOS в edge-cases может отличаться.

## Инвариант

Безопасный кросс-платформенный паттерн:
```bash
# Небезопасно (BSD/GNU edge-case divergence)
sed -E 's/pattern/& suffix/'

# Безопасно (одинаково на BSD и GNU)
sed -E 's/(pattern)/\1 suffix/'
```

## Митигация

1. В sed-скриптах использовать `\1` (capture group) вместо `&` в replacement
2. Тест на macOS в CI — или `brew install gnu-sed` + alias `gsed`
3. Для критических замен использовать `python3 -c` как platform-neutral альтернативу sed

## Связи

- DP.FM.033 (bash set -e arithmetic exit) — аналогичный паттерн platform-specific shell quirk
