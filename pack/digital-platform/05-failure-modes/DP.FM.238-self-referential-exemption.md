---
id: DP.FM.238
name: "Self-referential exemption: whitelist исключений в одном репо с аудитором"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-26
source: "peer-session 2026-06-26-26-wp436-claude-writer, turn 2; WP-436"
related:
  references: [DP.FM.237, DP.D.222]
  see_also: ["CODEOWNERS — контрмера"]
tags: [security-audit, exemption, whitelist, self-referential, codeowners]
---

# DP.FM.238 — Self-referential exemption: аудитор аудирует сам себя

## Паттерн

Whitelist исключений из security-проверки (`gate-exempt`) хранится в том же репо, что и код аудитора. Один PR добавляет нарушение и exemption для него одновременно.

## Пример

```
# Один PR содержит два файла:
# 1. handlers/bypass.py      ← нарушает gate (запрещённый вызов)
# 2. gate-exempt.yaml        ← добавляет "bypass" в список исключений
#
# gate-full-audit.py читает gate-exempt.yaml перед проверкой
# → bypass.py пропускается
# PR одобрен одним ревьюером → оба файла в main
```

## Механизм

1. `gate-full-audit.py` загружает `gate-exempt.yaml` из того же репо
2. PR изменяет оба файла в рамках одного коммита
3. Ревьюер видит два изменения, принимает одним решением (или под давлением)
4. Аудит исключает из проверки именно то нарушение, которое только что добавлено

## Почему опасен

- **Атомарный обход:** одно PR-действие вносит нарушение и легитимизирует его
- **Audit confidence:** владелец думает «аудит прошёл» — нарушение не обнаружено
- **Незаметен при ревью:** оба файла могут выглядеть «законными» по отдельности
- **Шаблонируется:** каждый последующий обход имеет готовый прецедент в истории PR

## Лечение

Whitelist исключений обязан быть вне юрисдикции вносящего нарушения:

1. **Отдельный защищённый путь** с `CODEOWNERS: @security-owner` (другой owner от основного кода)
2. **Отдельный репо** с owner-отличным от аудируемого
3. **Обязательный second-channel approve** (security-owner review, не standard PR review)

**Тест:** «Может ли один PR добавить нарушение и exemption для него без second-channel approve?» Да → аудитор аудирует сам себя.

## Связи

- Усиливает: DP.D.222 (non-blocking аудит = рекомендация)
- Смежный FM: DP.FM.237 (provenance вне репо)
- Контрмера: CODEOWNERS с security-owner на exemption-путь
- Метод верификации: DP.METHOD.154 (bypass-class taxonomy — класс «self-referential»)
