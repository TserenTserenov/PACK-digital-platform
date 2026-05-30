---
id: DP.M.240
name: "Self-recoverable tooling: SoT в репо + symlink/copy в writable PATH"
type: method
status: active
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-30
related:
  prevents: [DP.FM.097]
  see_also: [DP.D.101]
tags: [tooling, deployment, launchd, cron, distribution, self-recovery]
source: "WP-364 fork4 close (peer-session 2026-05-30-27, iwe-tg installation)"
schema_version: 1
---

# DP.M.240 — Self-recoverable tooling: SoT в репо + symlink/copy в writable PATH

## Суть метода

Pattern для distribution мелких CLI-инструментов агента (wrappers, alert-sender, helper-script) в персональной инсталляции IWE, чтобы они были:

- **(а)** доступны в PATH из любых вызывающих контекстов (cron, launchd, hook'и);
- **(б)** self-recoverable без sudo и без ручной правки `~/.zshrc` после переустановки / wipe;
- **(в)** source-of-truth жил в git-репо, не локально.

## Три требования к target-директории

1. **Writable текущим пользователем без sudo** — на macOS это `/opt/homebrew/bin/` для пользователя, владеющего Homebrew installation. На Linux — `~/.local/bin/`, если в PATH cron'а.
2. **Уже в PATH для launchd / cron / hook-process'ов** — это **отличается** от interactive shell PATH. Проверить через `launchctl getenv PATH` (macOS launchd) или `crontab -l` env.
3. **Persistent across reboots** — system-managed paths удовлетворяют этому автоматически.

## Рецепт

1. `scripts/<tool-name>` в git-репо (governance-репо: `DS-my-strategy/scripts/`) с правильным shebang и `chmod +x`.
2. `cp scripts/<tool-name> /opt/homebrew/bin/` ИЛИ `ln -sf $(pwd)/scripts/<tool-name> /opt/homebrew/bin/<tool-name>` в writable PATH-dir.
3. Restore-команда задокументирована в `_outcome.md` или `scripts/README.md` одной строкой:
   ```bash
   cp DS-my-strategy/scripts/iwe-tg /opt/homebrew/bin/ && chmod +x /opt/homebrew/bin/iwe-tg
   ```

## Антипаттерны

| Антипаттерн | Что плохо |
|-------------|-----------|
| Tool живёт **только** в `/usr/local/bin/` | требует sudo при переустановке после wipe → пользователь забывает шаг |
| Tool в `~/bin/` | не в launchd PATH → cron / hook'и его не видят, симптом «работает в терминале, не работает в фоне» |
| Hard-coded путь в каллере (`/Users/.../scripts/iwe-tg` в hook) | ломается при смене пользователя или перемещении репо |

## Тест применимости

«Нужен ли инструмент launchd'у / cron'у / hook'у без интерактивного shell environment?»
- **Да** → writable PATH-dir с известным launchd-PATH значением (этот метод).
- **Нет** (используется только из interactive shell) → достаточно `~/bin/` + `~/.zshrc`.

## Связи

- **Prevents DP.FM.097** (deployment path drift home vs repo) — сводит drift к одной copy-команде, повторяемой при переустановке, вместо ручной правки конфигов вне репо.
- **DP.D.101** (Shared Module Sharing: Symlink ≠ Submodule ≠ Vendor Copy) — соседняя ось: distribution **библиотек/модулей** между процессами. Этот метод — distribution **CLI-исполняемых**.

## Применимо к

`iwe-tg`, любые agent-side helper'ы (notify-script, lock-acquire, mcp-launcher), персональные CLI shim'ы.

## Прецедент

WP-364 fork4 close (2026-05-30): создан `DS-my-strategy/scripts/iwe-tg` (SoT в репо) + установлен в `/opt/homebrew/bin/iwe-tg` (writable без sudo, уже в launchd PATH). Восстановление после переустановки = одна команда из `_outcome.md`.
