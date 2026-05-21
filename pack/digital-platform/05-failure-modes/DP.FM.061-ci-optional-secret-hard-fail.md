---
id: DP.FM.061
title: CI Optional Secret Treated as Required
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-20
valid_from: 2026-05-20
sources:
  - commit 21a54a7 (docs, fix: CF Pages deploy — graceful skip when CLOUDFLARE_API_TOKEN missing)
related:
  distinguishes_from: []
  references: []
---

# DP.FM.061 — CI Optional Secret Treated as Required

## Описание

CI-шаг завершается `exit 1` при отсутствии опционального секрета. Для contributor без доступа к секрету — ложный блокер: PR не проходит CI, хотя основная функциональность работает.

«Опциональная зависимость не должна блокировать обязательные проверки.»

## Условие возникновения

- CI-шаг использует внешний токен/секрет
- Токен опциональный (деплой в 3rd-party, optional integration)
- Fork, contributor, или среда без секрета запускает CI

## Fix

```bash
if [ -z "$TOKEN" ]; then
  echo "Skip: TOKEN not set (optional dependency)"
  exit 0
fi
# ... deploy logic
```

Разделять обязательные секреты (отсутствие = `exit 1`) и опциональные (отсутствие = graceful skip, `exit 0` + сообщение).

## Тест (триггер распознавания)

«CI шаг падает из-за отсутствия токена? Токен нужен только для деплоя в 3rd-party?» → Да → заменить exit 1 на graceful skip.

## Применимость

- CF Workers / CF Pages deploy в CI
- Railway deploy в contributor-контексте
- Любая 3rd-party интеграция в open-source или multi-tenant CI
