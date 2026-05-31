---
id: DP.FM.114
type: failure-mode
pack: PACK-digital-platform
status: accepted
trust: high
epistemic_stage: observation
valid_from: 2026-05-31
source: WP-330 FDW regression 2026-05-31
---

# DP.FM.114 — Silent regression при копировании adapter без внешних зависимостей

## Симптом

Adapter/bridge скопирован в новую точку деплоя без сопутствующих определений внешних источников данных (FDW, connection-map, mount-path). Запросы выполняются без ошибок, но данные тянутся из неверного источника. Явного fail нет — диагностика затруднена.

## Причина

Adapter содержит implicit dependency на внешний data source (PostgreSQL FDW, S3-mount, NFS-path, external DB link). При копировании dependency не переносится автоматически. Adapter работает в «degenerated mode» — читает fallback source или встроенные defaults.

## Пример

`iwe-gateway-bridge.py` скопирован в новую деплой-точку без FDW-определений → запросы выполняются штатно, но данные тянутся из локальной БД вместо FDW-источника.

## Контрмера

Добавить dependency-smoke в deploy checklist: сразу после деплоя выполнить явный тест внешней зависимости (SELECT из FDW-таблицы, HEAD к внешнему API, ls mount-path). Fail smoke = rollback.

## Применимость

Любой adapter с внешними data source dependencies: FDW, database links, filesystem mounts, API integrations. Особенно критично при copy-deploy (не git-based).

## Связи

- DP.FM.016 (routing-decay) — близкий паттерн: implicit dependency на routing config
