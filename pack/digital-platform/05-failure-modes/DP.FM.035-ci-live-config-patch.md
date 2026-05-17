---
id: DP.FM.035
name: CI live-config patch — iteration debt от хрупких примитивов
type: failure-mode
domain: ci-cd
status: active
trust: validated
epistemic_stage: refined
valid_from: 2026-05-14
sources:
  - 14 мая 2026, 5 итераций fix Caddy patch workflow за 32 минуты (commits c811833, 9cf9bba, a9d63c6, 8414e44, 8001c63)
related:
  - DP.FM.024 git-pull-in-production
  - DP.FM.025 monorepo-multisvc-f1-violation
schema_version: 1
---

# DP.FM.035 — CI live-config patch: iteration debt

## Симптом

Серия из 3-5+ быстрых коммитов в один CI-workflow за < 1 час, патчащих один live-config (Caddy, nginx, traefik). Каждая итерация — попытка обойти конкретный failure mode без явной диагностики до запуска. Обычно завершается «случайным» успехом без понимания корневой причины.

## Три недостаточных примитива

### 1. Heredoc внутри YAML

```yaml
run: |
  cat > /etc/caddy/Caddyfile.d/site.conf <<EOF
  example.com {
    reverse_proxy localhost:8080
  }
  EOF
```

Хрупкости:
- escaping `$` (GitHub Actions expression vs shell variable)
- переносы строк (Windows CRLF в репо ломает heredoc)
- indentation (heredoc-body индент меняет содержимое файла)
- кавычки внутри heredoc-marker (`<<'EOF'` vs `<<EOF` — разное поведение expansion)

### 2. SSH-key choice без probe-step

CI-runner имеет несколько SSH-credentials (NOMAD, VKCS, prod-deploy и т.п.) с разным scope. Failure mode `Permission denied (publickey)` неотличим от `wrong host` или `wrong user`. Без probe-step `ssh user@host 'echo ok' || exit 1` ДО патча, неверный выбор ключа диагностируется только по логу финального шага.

### 3. Hardcoded путь к конфигу

`/etc/caddy/Caddyfile.d/` валиден сегодня, но обновление Caddy может перенести путь (FreeBSD vs Linux, package vs systemd-managed). Без diagnose-step'а `caddy_config_path=$(systemctl show caddy -p ExecStart | grep -oP -- '--config \K[^ ]+')` патч записывается в orphaned-файл.

## Корневая причина

CI live-config patch смешивает три ответственности: (1) **обнаружение** места конфига, (2) **аутентификация** на target host, (3) **трансформация** содержимого. Каждая может упасть, диагностика затруднена сложностью YAML-логов.

## Mitigation

- **Probe-first:** диагностический шаг ДО патча — `ssh ... 'cat <config-path>'` или `caddy validate`. Если probe падает — fail early с понятной ошибкой.
- **Sed > heredoc:** для замены секций — `sed -i 's|old|new|' file` с exact-match. Надёжнее heredoc, требует знать шаблон.
- **Декларативный config через git + SIGHUP:** альтернатива целиком — хранить конфиг в репо, deploy через `git pull && systemctl reload caddy`. Избегает SSH-патча live-конфига.

## Связи

- **Соседи:** DP.FM.024 (git-pull-in-production — другой anti-pattern «изменения live-state»), DP.FM.025 (monorepo-multisvc — другой класс CI-ошибок).
- **Альтернатива:** declarative config (NixOS, Ansible idempotent modules) — выводит patch за пределы CI.
