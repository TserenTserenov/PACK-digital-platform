---
id: DP.METHOD.121
type: method
domain: digital-platform
pack: PACK-digital-platform
status: draft
summary: "Административное удаление записи из immutable append-only журнала: 4-шаговая атомарная транзакция с SESSION_LOCAL bypass триггера только-для-последней-записи. Применимо к любому append-only хранилищу с GDPR-требованиями."
created: 2026-07-05
trust:
  F: 3
  G: domain
  R: 0.85
epistemic_stage: evidence
related:
  see_also: [DP.METHOD.101, DP.FM.199]
tags: [immutable-log, admin-delete, gdpr, postgresql, session-local, hash-chain, audit, append-only]
wp: WP-457
---

# Административное удаление записи из immutable-журнала (DP.METHOD.121)

## Описание

Паттерн «безопасного escape hatch» для административного удаления из append-only журнала с hash-chain. Применяется только для последней записи цепочки (иначе разрыв ломает всех downstream). Обязательны все четыре шага в одной атомарной транзакции.

## Условие применимости

- Append-only хранилище с GDPR-требованием права на забвение
- Требуется явная административная функция (не прямой DELETE)
- Хранилище защищено безусловным триггером `BEFORE UPDATE OR DELETE`

## IPO

**Вход:** идентификатор записи (должна быть последней в цепочке)

**Действие (4 шага, одна транзакция):**

1. **Guard-check:** подтвердить, что запись — последняя в цепочке. Если нет → EXCEPTION (удаление середины цепочки = нарушение целостности).
2. **Pointer rollback:** атомарно откатить таблицу-указатель `stream_tip` на предыдущую запись ДО удаления целевой.
3. **Audit entry:** записать факт удаления с причиной (GDPR-request, user_id, timestamp) в неприкосновенный журнал административных действий ДО самого DELETE.
4. **SESSION_LOCAL bypass + DELETE:** выставить `SET LOCAL bypass_immutable_trigger = 'on'` (видно только внутри текущей транзакции, не другим сеансам) → выполнить DELETE → транзакция завершается, SESSION_LOCAL сбрасывается.

**Выход:** запись удалена, цепочка не сломана, audit trail сохранён

## Инварианты

**Только последняя запись** — удаление из середины требует механизма «новой эпохи» (chain fork), не этого паттерна.

**SESSION_LOCAL, не SET (глобальный)** — глобальный bypass открывает брешь на весь сеанс для всех конкурентных запросов.

**Audit entry ДО DELETE** — если DELETE упадёт, audit entry будет виден → инцидент не потеряется.

## Связи

- DP.METHOD.101 (Append-only аудит-журнал с tamper-evidence) — описывает дизайн хранилища; данный метод — complementary escape hatch
- DP.FM.199 (Запрет прав на уровне роли недостаточен) — объясняет, почему нужен trigger + SESSION_LOCAL, а не только REVOKE
- WP-457 Ф12 — первый прецедент
