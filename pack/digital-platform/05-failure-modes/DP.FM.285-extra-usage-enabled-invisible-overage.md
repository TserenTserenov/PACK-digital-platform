---
id: DP.FM.285
name: "hasExtraUsageEnabled Invisible Overage (Невидимое автосписание за превышение квоты Claude Max)"
category: cost-control / observability
severity: high
status: active
summary: "Флаг hasExtraUsageEnabled: true в ~/.claude.json разрешает Anthropic автоматически списывать за превышение квоты подписки Claude Max. Флаг не виден в Claude Code UI — нужно проверять Anthropic Console. При расследовании регулярных API-расходов без явного кода-потребителя проверять этот флаг в первую очередь."
created: 2026-07-13
valid_from: 2026-07-13
related:
  see_also: []
tags: [cost-control, claude-code, anthropic, billing, config, subscription, observability]
source: "session-transcript 2026-07-13, WP-399 аудит расходов Anthropic API (git diff 5846c15f2)"
---

# [DP.FM.285] hasExtraUsageEnabled Invisible Overage

## Суть паттерна

Конфигурационный флаг `hasExtraUsageEnabled: true` в `~/.claude.json` разрешает Anthropic **автоматически списывать платежи за превышение квоты** подписки Claude Max. Флаг не отображается в интерфейсе Claude Code — его можно увидеть только в Anthropic Console (Usage → Billing) или прочитав JSON-файл напрямую. При отсутствии явного LLM-кода-потребителя на инфраструктуре — этот флаг становится первым кандидатом на источник регулярных неожиданных списаний.

## Инцидент

WP-399 аудит расходов Anthropic API: на подписке Claude Max 20x регулярные списания ~$21 без очевидного кода-потребителя. `hasExtraUsageEnabled: true` в `~/.claude.json` — флаг включён; Anthropic автоматически списывал за превышение квоты интерактивных сессий Claude Code.

## Механизм

1. При установке Claude Code или смене тарифа флаг может быть выставлен в `true` без явного подтверждения.
2. При превышении месячной квоты подписки Claude Code продолжает работать — Anthropic тихо выставляет счёт.
3. Флаг отсутствует в Claude Code UI → не виден при стандартном мониторинге.
4. Grep по кодовой базе не найдёт: флаг в конфиге пользователя, не в коде сервисов.

## Тест на провал

```bash
cat ~/.claude.json | grep hasExtraUsageEnabled
# true → FM.285 активен
```

## Детектор

При аудите неожиданных расходов Anthropic API:
1. `cat ~/.claude.json | grep -E "hasExtraUsage|extra.usage"` — проверить флаг.
2. Anthropic Console → Usage → Billing — посмотреть статью расходов.
3. Если расходы идут как "overage" без кода-потребителя — первый подозреваемый.

## Защита

1. **Явный opt-in:** проверить `hasExtraUsageEnabled` при первичной установке Claude Code и после смены тарифа.
2. **Мониторинг:** Anthropic Console Billing alerts — порог на месячные расходы.
3. **Отключение:** `hasExtraUsageEnabled: false` в `~/.claude.json` — Claude Code перестанет работать при превышении квоты вместо тихого дебетования.

## Связи

- Смежно с WP-399 (аудит расходов Anthropic API) — контекст обнаружения.
