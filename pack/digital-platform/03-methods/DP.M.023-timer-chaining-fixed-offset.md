---
id: DP.M.023
name: "Chaining nightly tasks через фиксированный offset"
type: method
status: active
valid_from: 2026-05-11
source: "WP-253 iwe-stage-evaluator (git diff iwe-server-config commit 2c7d16e, 2026-05-11)"
summary: "Зависимые ночные задачи (producer → consumer) запускаются с фиксированным N-минутным offset вместо явной зависимости After=/ExecStartPost. Устойчив к задержкам producer'а."
related:
  uses: []
  informs:
    - DP.ARCH.001
---

# DP.M.023 — Chaining nightly tasks через фиксированный offset

> **Контекст:** Планирование зависимых ночных задач, где задача-потребитель обрабатывает данные,
> подготовленные задачей-поставщиком за цикл до.

## §1 Паттерн

Задача-потребитель стартует через **фиксированный N минут** после задачи-поставщика.

```
producer: OnCalendar=*-*-* 04:30:00 UTC
consumer: OnCalendar=*-*-* 04:35:00 UTC   # +5 мин
```

Альтернативы: `After=` / `ExecStartPost=` в systemd (жёсткая зависимость).

## §2 Когда применять

| Ситуация | Паттерн |
|----------|---------|
| Consumer читает данные producer'а, задержка данных допустима (несколько минут) | Фиксированный offset ✅ |
| Consumer ДОЛЖЕН дождаться завершения producer'а | `After=` / `ExecStartPost` |
| Producer может занимать переменное время (>N мин) | Увеличить offset или использовать `After=` |

## §3 Преимущества и риски

**Преимущества:**
- Простота конфигурации: два независимых .timer файла
- Устойчив к задержкам producer'а: если producer задержался на < N мин, consumer получит свежие данные
- Нет жёсткой зависимости: можно запускать consumer вручную без запуска producer'а

**Риск:** Если producer задержится > N мин → consumer читает данные предыдущего цикла (stale data).
Правило: offset должен быть ≥ P95 времени выполнения producer'а + buffer.

## §4 Связи

- **DP.ARCH.001** — архитектурные принципы платформы: chaining применяется в overnight worker pipeline
- **DS docs** — конкретные времена (04:30/04:35 МСК), имена сервисов, .timer файлы

---

*Создано 2026-05-11 на основе реализации iwe-stage-evaluator (WP-253).*
