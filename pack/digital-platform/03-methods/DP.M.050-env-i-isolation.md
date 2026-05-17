---
id: DP.M.050
title: "env -i Изоляция для Smoke-теста Promote-скриптов"
type: method
pack: PACK-digital-platform
domain: digital-platform
tags: [smoke-test, env-isolation, promote, template-testing, shell-scripts]
valid_from: 2026-05-16
status: active
uses: [DP.M.019]
---

# DP.M.050 — env -i Изоляция для Smoke-теста Promote-скриптов

## Определение

Паттерн изоляции окружения при smoke-тесте promote-скриптов: запуск в `env -i` с минимальными переменными, имитирующими fresh-install пользователя.

## Проблема

Promote-скрипты, протестированные в реальном окружении автора, проходят smoke — но шаблон может не работать при fresh install, так как переменные резолвятся реальные, не плейсхолдерные.

## Метод

**Шаг 1.** Провалидировать хардкоды (grep на личные пути/имена).

**Шаг 2.** Запустить в изолированном окружении:

```bash
env -i \
  HOME=/tmp/iwe-smoke-$$ \
  PATH=/usr/local/bin:/usr/bin:/bin \
  IWE_GOVERNANCE_REPO=DS-strategy \
  bash promote-script.sh
```

**Шаг 3.** Интерпретировать exit-коды:
- `exit 127` = зависимость сломана (команда не найдена) → **блокер**, не промотировать
- `exit 0` или `exit 1` = приемлемо (штатное завершение или soft-fail)

## Тест применимости

«Работает у автора» ≠ «работает у пользователя» — признак отсутствия env-изоляции в smoke.

## Связи

- Дополняет DP.M.019 (script-promotion-process): шаг 5 smoke-test из 7 шагов
- Устраняет failure mode DP.FM.017 (asymmetric-env-cleanup): env -i начинает с чистого листа
- Аналог: `npm ci --ignore-scripts` как изолированный install

## Источник

WP-5 session-transcript 2026-05-16 + git diff FMT-exocortex-template (ed74b9e).
