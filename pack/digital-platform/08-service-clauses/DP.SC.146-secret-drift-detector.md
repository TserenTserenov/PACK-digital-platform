---
id: DP.SC.146
title: "Secret Drift Detector — сканер инсталляций секрета по всем слоям инфраструктуры"
domain: DP.IWE
subdomain: security
status: active
created: 2026-05-14
related_wp: [315, 212]
related_ar: [AR.205]
implements:
  - AR.205-rotation-verify-pass
realized_by:
  - script: FMT-exocortex-template/scripts/iwe-grep-secret.sh
  - script: FMT-exocortex-template/scripts/check-setup-update-parity.sh
  - inventory: DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/security-posture.md §6 Secret Inventory
---

# DP.SC.146: Secret Drift Detector

## Обещание (Promise)

По входу `<secret-value>` система возвращает:
- **N** — количество мест, где секрет всё ещё инсталлирован
- **Список мест** с layer-classification (env / cloud / pg / smoke)
- **Exit code non-zero** если N ≥ 1

По входу `<setup-script>` и `<update-script>` система проверяет паритет ключевых паттернов и возвращает дельту.

### Триггер

- **Ротация credential:** `ALTER ROLE`, `ALTER USER MAPPING`, `wrangler secret put`, `railway variables set`
- **Регулярный аудит:** VR.R.002 Аудитор при Month Close
- **Пред-релиз:** pre-commit hook при изменении `setup.sh` или `update.sh`
- **Ручной:** инженер запускает `iwe-grep-secret.sh '<value>'` или `check-setup-update-parity.sh`

### Входы

- **secret_value:** строка для поиска (пароль, токен, ключ)
  - Передаётся через stdin или аргумент в кавычках (не через env — предотвращает leak в `ps`)
- **layer_filter:** опционально `--layer env|cloud|pg|smoke` (по умолчанию all)
- **inventory_path:** путь к `security-posture.md §6` (по умолчанию авто-обнаружение)

### Выходы

- **stdout:** таблица «layer / location / hit-count»
- **stderr:** прогресс-лог (без самого секрета)
- **exit code:**
  - `0` — N = 0 (ни одного hit)
  - `1` — N ≥ 1 (есть hits, требуется ручная проверка)
  - `2` — ошибка инфраструктуры (нет ssh, нет psql, нет доступа к Railway/CF)
  - `3` — parity mismatch (только для `check-setup-update-parity.sh`)

### Инварианты

1. **False-negative cost > false-positive cost:** лучше лишний hit, чем пропущенный. Сканер не использует коротких regex'ов, которые могут filter out совпадения.
2. **Секрет не логируется:** в stdout/stderr/логах — только hit-count и location-identifier, не само значение.
3. **Полнота ≥ скорости:** если слой недоступен (нет ssh к tsekh-1) — exit 2, а не silent skip.
4. **Inventory = source-of-truth:** новый секрет/сервис добавляется в `security-posture.md §6` прежде чем сканер начнёт его видеть.

## Сценарии использования

### UC-1: Ротация Neon-пароля (AR.205 probe)

```bash
# Step 1: старый пароль ещё где-то?
iwe-grep-secret.sh 'old_password_123' --layer all
# → Layer 1 (env):  3 hits  ✗
# → Layer 2 (cloud): 2 hits  ✗
# → Layer 3 (pg):    1 hit   ✗
# exit 1 → Close блокирован

# Step 2: после применения rotation
cat new_password | iwe-grep-secret.sh --layer all
# → 0 hits everywhere → exit 0 → Close разрешён
```

### UC-2: Проверка setup.sh ↔ update.sh parity

```bash
check-setup-update-parity.sh
# → Pair setup.sh↔update.sh: 4/4 patterns OK ✓
# → Pair migrate-001.sh↔migrate-002.sh: 1/3 patterns MISSING → exit 3
```

### UC-3: Ежемесячный аудит (VR.R.002)

```bash
# Для каждого секрета из inventory:
for secret in $(jq -r '.secrets[].last_value' inventory.json); do
  iwe-grep-secret.sh "$secret" --layer all || echo "DRIFT: $secret"
done
```

## Архитектура

### Слои сканирования

| Слой | Реализация | Права | Частота |
|------|-----------|-------|---------|
| 1. Env-файлы | `grep -r` по `~/.secrets/`, `~/.env`, `~/IWE/**/.env`, ssh tsekh-1 | local + ssh | on-demand / AR.205 |
| 2. Cloud env | Railway API / `railway variables`, CF `wrangler secret list` | RAILWAY_TOKEN, CF_API_TOKEN | on-demand / AR.205 |
| 3. PG metadata | `psql -c "SELECT srvname, umoptions FROM pg_user_mapping..."` | neondb_owner или fdw_reader | on-demand / AR.205 |
| 4. Smoke-tests | Подключение через роль, FDW-функция, API call | соответствующие credentials | on-demand only |

### Расширяемость

- Новый слой: добавить функцию `_scan_layer_<name>()` в `iwe-grep-secret.sh` + строку в inventory
- Новый сервис: добавить в `security-posture.md §6` + сканер подхватит при следующем запуске
- Новый парный скрипт: добавить в `.claude/parity-contract.yaml`

## Роли

- **VR.R.002 Аудитор** — ежемесячный запуск полного probe, обновление inventory
- **R24 Аудитор (расширение)** — фаза Sec-Sweep: рутинная проверка secret-инвентаря при каждом аудите
- **Любой инженер IWE** — ручной запуск при ротации

## Метрики

- **Scan latency:** < 30s для Layer 1-3 (локальные + ssh + psql)
- **Scan latency (cloud):** < 60s (Railway API + CF Workers)
- **False-negative rate:** целевой 0% (если инфраструктура доступна)
- **Coverage:** 100% сервисов из inventory

## Реализация

- **Фаза 1 (WP-315 Ф1):** Service Clause + bootstrap inventory + role mapping — ✅
- **Фаза 2 (WP-315 Ф2):** `iwe-grep-secret.sh` MVP (Layer 1 + Layer 3) — ⏳
- **Фаза 3 (WP-315 Ф3):** Cloud layers (Layer 2) — ⏳
- **Фаза 4 (WP-315 Ф4):** `check-setup-update-parity.sh` — ⏳
- **Фаза 5 (WP-315 Ф5):** Smoke-test Test 10 (full mode) — ⏳
- **Фаза 6 (WP-315 Ф6):** `sync_fdw_credentials.sql` — ⏳
- **Фаза 7 (WP-315 Ф7):** E2E rotation simulation — ⏳
