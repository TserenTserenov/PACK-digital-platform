---
id: DP.FM.017
name: Asymmetric Env Cleanup (Асимметричная очистка env-переменных)
category: testing
severity: major
status: active
summary: "Smoke-test устанавливает несколько env-переменных с эфемерными путями (/tmp/iwe-smoke-*), но cleanup сбрасывает не все → ночные/non-interactive запуски падают с path-ошибками."
created: 2026-05-11
valid_from: 2026-05-11
related:
  extends: [DP.FM.009]
  see_also: [DP.FM.002]
tags: [testing, smoke-test, env, cleanup, ci, shell, launchd]
source: "git diff FMT-exocortex-template (commit a3064ab, WP-5), 2026-05-11"
---

# [DP.FM.017] Asymmetric Env Cleanup

## Суть паттерна

Smoke-test устанавливает несколько env-переменных с эфемерными путями (`/tmp/iwe-smoke-*`). Cleanup/guard-функция сбрасывает только часть из них. Незачищенные переменные «протекают» в следующие процессы — ночной cron/launchd получает env с `/tmp/iwe-smoke-*` путями, которые уже не существуют.

**Конкретный инцидент:** Guard сбрасывал `IWE_TEMPLATE=/tmp/iwe-smoke-*`, но не `IWE_WORKSPACE=/tmp/iwe-smoke-*`. setup-agent падал с «исходный файл не найден».

## Механизм

1. Smoke-test выставляет `IWE_TEMPLATE=/tmp/iwe-smoke-e2e-XXXXX` и `IWE_WORKSPACE=/tmp/iwe-smoke-e2e-XXXXX`
2. Guard проверяет и сбрасывает только `IWE_TEMPLATE`
3. `IWE_WORKSPACE` остаётся в env после smoke-test
4. Ночной launchd-тригер запускает setup-agent → видит `IWE_WORKSPACE=/tmp/iwe-smoke-e2e-XXXXX` → path не существует → ошибка

## Диагностика

| Симптом | Описание |
|---------|----------|
| Локально тест проходит | Interactive shell — guard корректен |
| Ночной запуск падает с path-ошибкой | non-interactive, leaked env-переменная |
| Сообщение вида «исходный файл не найден» | Указывает на `/tmp/iwe-smoke-*` путь |

## Правило предотвращения

**Каждая env-переменная, выставляемая smoke-тестом с эфемерным путём, должна иметь симметричный guard при cleanup.**

```bash
# Шаблон guard-функции — перечислять ВСЕ переменные атомарно
guard_smoke_env() {
    for var in IWE_TEMPLATE IWE_WORKSPACE IWE_RUNTIME IWE_CONFIG; do
        if [[ "${!var}" == /tmp/iwe-smoke-* ]]; then
            unset "$var"
        fi
    done
}
```

**При добавлении новой переменной в smoke-test:** сразу добавить её в список guard-функции (атомарное правило).
