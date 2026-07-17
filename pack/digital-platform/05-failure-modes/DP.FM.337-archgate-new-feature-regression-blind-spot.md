---
id: DP.FM.337
name: "ArchGate проверяет новую функцию, не регрессию соседних сервисов"
type: failure-mode
status: active
valid_from: 2026-07-11
source: "WP-5 DP.SC.190 регрессия; commit fc5e5b7; обнаружена живым тестом 2026-07-11"
related:
  see_also: [DP.FM.239]
tags: [archgate, regression, neighbor-service, security, authorization, scope-blindspot]
---

# DP.FM.337 — ArchGate не защищает от регрессии соседних сервисов

## Симптом

Новая функция проходит архитектурную проверку (ArchGate). PR мержится. Соседний
endpoint, изменённый в том же PR, теряет проверку авторизации. Обнаруживается
tолько живым smoke-тестом.

## Механизм

ArchGate задаёт вопрос: «Корректно ли НОВОЕ обещание (DP.SC.NNN)?»
Он не задаёт: «Целы ли СОСЕДНИЕ обещания, реализация которых затронута в PR?»

Реализация новой функции касается shared-компонента (endpoint, middleware,
authorization layer) → соседний endpoint затрагивается, но выходит за scope
архитектурной проверки.

## Пример

```
PR: добавить endpoint /ai-external (DP.SC.190)
ArchGate: PASS (новый endpoint спроектирован корректно)
Побочный эффект: middleware-рефакторинг удалил authorize() из /users
Результат: /users отдаёт 200 без авторизации — регрессия security
```

## Последствие

Security-регрессия в production; обнаружена не ревью и не ArchGate, а живым
testом через 8 дней.

## Фикс

При PR, затрагивающем shared endpoints / middleware / authorization:
1. **Явный список контактных точек** из diff (`grep -r authorize diff`)
2. **Regression smoke-тест ВСЕХ** изменённых точек, не только новой функции
3. Для security-critical: проверка авторизации → отдельный шаг перед merge

## Тест

«ArchGate пройден — затронутые в PR соседние эндпоинты тоже проверены?"
Нет явного шага → вероятно DP.FM.337.

«commit_msg содержит "lesson_closed" / "feature done", а diff затрагивает
middleware?» → обязателен grep по diff на security-функции.

## Отличие от DP.FM.239

DP.FM.239 = архитектурное решение не версионировано (нет audit trail).
DP.FM.337 = ArchGate корректно проверяет новое решение, но слеп к regression
соседних.
