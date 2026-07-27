---
id: DP.M.062
name: Bridge-backfill через shared identifier при blocked identity-provider
name_ru: Bridge-backfill через shared identifier при blocked identity-provider
name_en: Bridge Backfill via Shared Identifier When Identity Provider Blocked
type: method
status: active
summary: "При cross-system identity-миграции, когда new identity-provider (ORY, OAuth, SSO) недоступен или unblocked-deploy откладывается — не блокировать миграцию полностью. Искать существующий shared identifier (id, present в обеих БД: legacy + new) и проводить linking через него. Покрытие partial + weekly retry для непокрытых. Тест: «есть ли поле, присутствующее в обеих системах?» Да → backfill через него."
created: 2026-05-17
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: emerging
related:
  uses: []
  references: []
  realized_by: []
tags: [identity, migration, backfill, sso, ory, oauth, cross-system, workaround]
wp: WP-296
---

# Bridge-backfill через shared identifier при blocked identity-provider (DP.M.062)

## 1. Контекст

Cross-system identity-миграция (legacy LMS → modern users-table, OAuth-провайдер rotation, SSO-провайдер migration) часто блокируется external API: новый identity-провайдер недоступен, deploy откладывается, vendor-side bug. Альтернатива «ждать unblock» дорогая — теряются недели realtime-данных.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость partial coverage сейчас ↔ полнота canonicalization identifier'а | §2 требует backfill «сразу»; но спешка с bridge через email/phone может пропустить canonicalize-шаг (lower-case, strip whitespace) — выигрыш в скорости оборачивается дублями (§5 анти-паттерн 3) |
| Доступность identifier'а ↔ его надёжность как bridge | Из трёх кандидатов §2 (aisystant_suser_id / email / phone_number) email/phone кажутся универсальнее — доступны почти всегда, но требуют доп. проверки на case-sensitivity и дубликаты, тогда как более редкий aisystant_suser_id надёжнее, но покрывает не всех пользователей |
| Partial coverage сейчас ↔ дисциплина weekly retry-цикла | Пример §8 даёт 56.9% linked сразу — выглядит достаточным результатом, чтобы закрыть задачу, но без непрерывного weekly retry (§5 анти-паттерн 2) оставшиеся 43% никогда не будут связаны |

## 2. Правило

**Не блокировать миграцию полностью пока identity-API не готов.** Искать existing shared identifier — поле, присутствующее **и в legacy, и в new** системе, без обращения к external API:

- `aisystant_suser_id` в LMS + в `users.account_id` (бывший случай WP-296)
- `email` (если совпадает в обеих БД) — но проверить case-sensitivity и canonicalization
- `phone_number` (если уникален и присутствует в обеих)

Backfill через shared identifier → partial coverage **сразу**, остальные → weekly retry (пока identifier появится в new системе при следующем логине пользователя).

## 3. Тест применимости

«Есть ли поле, присутствующее в обеих системах (legacy + new), которое можно использовать как bridge?»

- **Да** → backfill через него, не ждать identity-provider
- **Нет** → дождаться unblock identity-provider; параллельно искать альтернативный bridge (например, восстановить shared identifier через ETL из third party)

## 4. Различение с full-API-backfill

| Подход | Когда | Покрытие | Retry-цикл |
|--------|-------|----------|-------------|
| **Bridge backfill** | Identity-API blocked/unstable | Partial (~50-70% сразу) | Weekly retry для непокрытых |
| **Full-API backfill** | Identity-provider stable, есть direct API | 100% single-pass | Не нужен |

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `epistemic_stage: emerging`: пометка `tentative` по прецеденту WP-448 Ф12._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Список кандидатов §2 читается как приоритетный порядок, а не как набор для проверки | При выборе identifier внимание съезжает на первое поле, совпавшее с прошлым прецедентом (`aisystant_suser_id`, WP-296), вместо того чтобы заново проверить для ТЕКУЩЕЙ пары систем, какое из трёх полей списка реально присутствует и пересекается в обеих базах |
| _(tentative)_ Процент из §8 воспринимается как ожидаемая норма покрытия | При планировании нового backfill внимание сверяется с конкретной цифрой прошлого случая («1439/2527, 56.9%») как с ориентиром «нормального» результата, а не с фактической пересекаемостью identifier в текущей паре систем — низкое или высокое реальное покрытие интерпретируется через призму чужого прецедента, а не оценивается само по себе |

## 5. Анти-паттерн

| Анти-паттерн | Симптом | Лечение |
|--------------|---------|---------|
| **Блокировать миграцию пока identity-API не unblocked** | Недели realtime-данных потеряны | §2 — bridge backfill через shared id |
| **One-shot bridge без retry-цикла** | Пользователи, не залогинившиеся за окно backfill, навсегда unlinked | Weekly retry-job для непокрытых |
| **Bridge через non-canonical identifier (email с разной caps)** | Дубли + false-negatives | Canonicalize ДО linking (lower-case email, strip whitespace) |

## 6. Применимость

- **ORY/Auth0/Keycloak migration** — пока новый identity-provider не unblocked
- **OAuth rotation** — пока новый client_id не deploy'нут
- **Cross-DB identity reconciliation** — legacy CRM ↔ new analytics DB

## 7. Связь с другими методами/lessons

- **`memory/lessons_cross_layer_identity_sync.md`** — identity-операция обновляет N слоёв (правило). Этот метод даёт recovery-strategy при blocked identity-provider
- **`memory/lessons_credential_rotation_in_chat.md`** — credential rotation в чате = риск; bridge backfill безопаснее (не требует передачи токенов в чате)

## 8. Пример (WP-296, 17 мая 2026)

Ф4 шаг 1: ORY SSO от Andrey ✅ готов, но deploy на club.ony требует unblock. Параллельно — bridge backfill через `aisystant_suser_id`:

```python
# pseudo
for user in club_users:
    if aisystant_suser_id := user.aisystant_suser_id:
        if persona := persona.find_by_aisystant_suser_id(aisystant_suser_id):
            link(club_user_id, persona.id)
            linked += 1
```

Итог: 1439/2527 linked (56.9%). 1088 непокрытых → weekly retry (когда пользователь сам залогинится и shared ID появится через bot).

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
