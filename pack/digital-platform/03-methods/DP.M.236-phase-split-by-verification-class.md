---
id: DP.M.236
name: "Разделение фазы РП по классу верификации (trivial/closed-loop/open-loop/problem-framing)"
type: method
status: draft
valid_from: 2026-05-30
pack_refs:
  - DP.M.060  # atomic VDV step — atomic-фаза по одному классу
  - DP.M.163  # deferred-phase-finalization-checkpoint — отложенная финализация
domains: [wp-management, phase-decomposition, verification-classes]
last_updated: 2026-08-01
---

# DP.M.236 — Разделение фазы РП по классу верификации

## Описание

Метод декомпозиции фазы РП по характеристике верификации каждой под-задачи (trivial / closed-loop / open-loop / problem-framing). Closed-loop + trivial закрываются в текущей сессии; problem-framing выделяется в отдельную фазу с pre-articulated открытыми вопросами и маркером «не блокирует closed-loop».

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость закрытия closed-loop/trivial ↔ качество problem-framing | Если ждать, пока решится policy-вопрос, fix застревает в backlog; если выделять problem-framing слишком рано, мелкие задачи размазываются по фазам и растёт overhead handoff'ов |
| Атомарность фазы ↔ разнообразие классов верификации | Зонтичная фаза удобна как единый контейнер, но смешивает классы; split делает каждый класс чистым, но увеличивает число фаз и точек передачи |
| Полнота сессионного закрытия ↔ непрерывность отложенных задач | Хочется закрыть сессию с максимум done, но это требует чётких checkpoint для open-loop/problem-framing, иначе они выпадают из внимания |

## Когда применять

- Фаза РП содержит ≥3 под-задачи разных классов верификации
- Closed-loop fix готов, но policy-decision ждёт peer-session / ArchGate
- Trivial lesson обновлён, но широкий runtime gate требует обсуждения

## Антипаттерн

Держать зонтичную фазу `pending` пока завершится самая медленная под-задача (problem-framing). Closed-loop fix теряется в backlog, runtime-gate ждёт debate, lesson не маршрутизируется в memory/.

## Алгоритм

1. Перед close/review фазы — пройти список под-задач и классифицировать каждую:
   - **trivial** — обновление документа, лога, lesson-файла
   - **closed-loop** — runtime check, smoke-test, verifiable в той же сессии
   - **open-loop** — требует данных за пределами сессии (метрики, бэкап, A/B)
   - **problem-framing** — требует policy-решения или peer-discussion
2. Закрыть **trivial + closed-loop** под-задачи в текущей сессии (`status: done`)
3. **Open-loop** → checkpoint (см. DP.M.163), ждать данных
4. **Problem-framing** → выделить **отдельную фазу** с маркером:
   - `target_close: TBD (не блокирует <RPA1>)`
   - pre-articulated открытые вопросы (см. DP.M.238)

## Инвариант

- Closed-loop fix не блокируется ожиданием problem-framing
- Каждая под-задача завершается в фазе своего класса, не в зонтичной
- Pre-articulated вопросы фиксируются при выделении problem-framing phase, не позже

## Тест применимости

«Можно ли сделать эту под-задачу до peer-session без новой информации?» Да → closed-loop, делать сейчас. Нет → выделить в отдельную фазу с явными открытыми вопросами.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — черновик: пометка `tentative` по прецеденту SA.METHOD.001 (WP-448 Ф12)._

| Bias | Direction of distortion |
|------|--------------------------|
| Оптимистичная классификация | Под-задача записывается closed-loop («проверю тестом»), хотя проверка требует данных вне сессии — реальный open-loop; фаза закрывается по галочке, а проверка молча не происходит |
| Внимание остаётся с быстрой фазой | Trivial и closed-loop дают done-галочки сразу; выделенная problem-framing фаза с `target_close: TBD` уходит из поля зрения — маркер TBD не имеет собственного триггера возврата, и split превращается в способ забыть трудное |

## Источник

WP-7 RPA → RPA-policy split, сессия 2026-05-30 (commit `86e2388f`). Зонтичная фаза RPA блокировала closed-loop RPA1 + trivial RPA4 ожиданием problem-framing RPA2/RPA3 → split на RPA + RPA-policy.

---

> 2026-08-03 — дозакрытие миграции на обогащённый формат (Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6б). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
