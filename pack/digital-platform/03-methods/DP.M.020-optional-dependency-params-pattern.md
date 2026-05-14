---
id: DP.M.020
name: Паттерн необязательной зависимости скрипта через params.yaml
type: method
status: draft
summary: "Паттерн проектирования shell-скриптов с опциональными внешними зависимостями: ключ в params.yaml с дефолтом '' (пустая строка), graceful skip при пустом значении, warning+exit 1 при несуществующем пути. Три обязательных smoke-кейса."
created: 2026-05-11
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: validated
related:
  uses: []
  references: [DP.M.019]
tags: [params-yaml, optional-dependency, graceful-skip, script-design, shell]
---

# Паттерн необязательной зависимости скрипта через params.yaml

## 1. Определение

Паттерн проектирования shell-скрипта, у которого есть внешняя зависимость (путь к репо, CLI-инструмент, внешний сервис), но использование этой зависимости не является обязательным для базовой функции скрипта.

## 2. Структура

**params.yaml:**
```yaml
knowledge_repo: ""   # пустая строка = зависимость не настроена
```

**Скрипт:**
```bash
KNOWLEDGE_REPO=$(yq '.knowledge_repo' "$PARAMS_FILE")

if [ -z "$KNOWLEDGE_REPO" ]; then
  echo "knowledge_repo не задан в params.yaml — пропускаю шаг X. Укажите путь для активации."
  exit 0
fi

if [ ! -d "$KNOWLEDGE_REPO" ]; then
  echo "WARNING: knowledge_repo=$KNOWLEDGE_REPO не существует" >&2
  exit 1
fi

# основная логика
```

## 3. Три обязательных smoke-кейса

| Кейс | Входное состояние | Ожидаемый результат |
|------|------------------|---------------------|
| Пустой ключ | `knowledge_repo: ""` | Вывод подсказки + exit 0 (не блокирует pipeline) |
| Несуществующий путь | `knowledge_repo: /no/such/dir` | WARNING в stderr + exit 1 |
| Штатный запуск | Корректный путь | Ожидаемый результат скрипта |

## 4. Различение

**Этот паттерн ≠ graceful degradation (asyncio):** здесь статический config-guard на входе скрипта, а не runtime-recovery при сбое уже запущенной операции. Применяется при `startup`, не при `execution`.

## 5. Применимость

Любой shell-скрипт с опциональными внешними зависимостями: knowledge-repo, personal-pack, WakaTime CLI, Obsidian vault, внешний API с опциональным доступом.

## 6. Связи

- **DP.M.019** — процесс промоции скрипта (использует этот паттерн на шагах 2-3).
