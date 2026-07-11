---
id: DP.METHOD.168
name: "ResidencyGate: статическая декларация потребности данных + динамическое состояние согласия"
type: method
pack: PACK-digital-platform
domain: digital-platform / privacy
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-transcript 2026-07-10-02-wp475-rezidentnost-f2 + git diff DS-my-strategy (commit 348c83ba)"
related:
  concepts: [DP.ARCH.005, DP.D.028, DP.D.035]
schema_version: 1
---

# DP.METHOD.168 — ResidencyGate: статическая декларация потребности данных + динамическое состояние согласия

## Описание

Паттерн для любой платформенной функции, которой нужны персональные данные. Разделяет два независимых уровня:

- **Статическая декларация** (`data_needs`): что функция запрашивает — git-committed, версионируется во frontmatter SKILL.md или манифест-блоке bash-хука
- **Динамическое состояние согласия** (`current/data-residency.yaml`): выдано ли согласие пользователем — gitignored, не покидает IWE

## Алгоритм

1. Функция декларирует `data_needs` при деплое (git-committed, виден при code-review):
   ```yaml
   data_needs:
     - type: telegram_id
       purpose: "delivery channel"
     - type: learning_history
       purpose: "personalization"
   ```
2. Локальный файл `current/data-residency.yaml` (gitignored) хранит состояние по каждому типу данных:
   ```yaml
   telegram_id: granted
   learning_history: not_asked
   ```
3. **Две точки запроса согласия:**
   - *Activation-time* — автономные функции (запрашивается при старте, если статус `not_asked`)
   - *Lazy* — интерактивные разовые функции (при первом реальном использовании данных)
4. Функция выполняется только если все требуемые типы = `granted`

## Аналогия

Аналог Extensions Gate: L1 generic hook (декларация потребности, версионируется) + L3 пользовательская конфигурация (состояние согласия, приватна). Один слой версионируется в git, другой — остаётся локальным.

## Применение

Любая privacy-sensitive функция платформы с несколькими источниками персональных данных (типы 2.1–2.4 по DP.D.028). Применимо к агентским скиллам, cron-хукам, MCP-инструментам с доступом к личным данным.

## Источник

WP-475 Ф2; session-transcript 2026-07-10-02-wp475-rezidentnost-f2; git diff DS-my-strategy (commit 348c83ba).
