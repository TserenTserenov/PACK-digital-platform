---
id: DP.SC.162
name: External Session Request
name_ru: Внешний запрос сессии
name_en: External Session Request
type: sc
status: draft
layer: L4-Platform
summary: "Пилот отправляет запрос из Telegram → платформа инициирует сессию Claude Code в VS Code → результат возвращается в Telegram. SLA: acknowledgment P95≤10с, completion P95≤45с (light)."
consumer: R14 Заказчик — пилот вне рабочего стола
created: 2026-05-27
updated: 2026-05-27
related:
  differs_from: DP.SC.135   # Agent Inbox — batch, due/template/acceptance
  differs_from_2: DP.SC.013  # Work Session — in-IDE, разный потребитель
  uses:
    - DP.ROLE.061             # External Session Adapter (исполнитель)
    - DP.SC.135               # Agent Inbox — НЕ нарушать (отдельная папка sessions/)
  see_also:
    - DP.SC.161               # Session Memory Injector — вызывается при старте сессии
wp: WP-358
---

# [DP.SC.162] Внешний запрос сессии

## Правило (инвариант)

> Нарушение любого = провал SC.

1. **Идемпотентность.** Composite key `(tg_chat_id, message_id)` — один Telegram-message создаёт ровно одну сессию. Повторный retry при сбое не порождает дубликат.

2. **Acknowledgment SLA.** Бот отвечает «Сессия #N запущена» в Telegram за P95 ≤10с с момента отправки команды. Acknowledgment не зависит от длительности обработки.

3. **Completion SLA.** P95 ≤45с для запросов `category: light` (ожидаемый ответ ≤200 токенов). Для `category: heavy` — async-режим: прогресс-нотификации каждые 15с, финальный ответ без гарантии SLA.

4. **PII guard.** Флаг `--private` в команде → transcript не коммитится в git, хранится только в локальной SQLite (`~/.iwe/sessions.db`). В SESSION-файл пишутся только метаданные (session_id, status, timestamps).

5. **Timeout + Heartbeat.** `max_session_duration = 15 минут` (hard cut-off). Heartbeat диспетчера (self-check): ping в SQLite каждые 30с; miss >90с → статус `failed` + TG alert. Cancellation contract: `/cancel <session_id>` от пилота → SIGTERM диспетчеру → partial transcript сохраняется → статус `cancelled`.

6. **Cleanup.** SESSION-`<id>`.md и transcript архивируются в `inbox/agent/sessions/archive/` через 24ч или немедленно при флаге `--archive-on-done`. Папка `sessions/` не является постоянным хранилищем.

**Failure mode:** VS Code не открыт или диспетчер не запущен → TG уведомление «Среда не запущена, запустите VS Code». Тихий fail запрещён.

---

## Обещание

**Кому:** Пилот (Заказчик, R14) — вне рабочего стола (метро, обед, прогулка).

**Зачем:** Идея или запрос, возникший вне IDE, не теряется и не ждёт возврата за компьютер. Платформа сама инициирует сессию и доставляет результат.

**Что получит:**
- Acknowledgment в Telegram ≤10с (подтверждение, что запрос принят)
- Результат сессии Claude Code — в том же Telegram-чате
- Audit trail — SESSION-файл в git (если не `--private`)
- Graceful failure — явное сообщение если среда недоступна

**Критерий приёмки:** end-to-end от `/claude <текст>` в Telegram до ответа в Telegram, P95 latency ≤45с для light-запросов, 10/10 smoke-тестов зелёные.

---

## Реализующие роли и сервисы

| Компонент | Роль | Что делает |
|-----------|------|-----------|
| Ingress Adapter (aist-bot, cloud) | DP.ROLE.061 §Ingress | Принять `/claude`, авторизовать, записать SESSION-файл |
| Egress Adapter (dispatcher, local) | DP.ROLE.061 §Egress | Обнаружить SESSION-файл, запустить Claude Code, вернуть ответ |
| Session Memory Injector | DP.SC.161 | Pre-flight: инжектировать контекст из iwe_memory.db |
| Agent Inbox | DP.SC.135 | Смежный сервис — НЕ смешивать (разный контракт) |

---

## Пользовательский путь

| # | Шаг | Кто | Результат |
|---|-----|-----|----------|
| 1 | Отправить `/claude [--private] <текст>` в @aist_pilot_bot | Пилот | — |
| 2 | Проверить `tg_chat_id ∈ allowed_list` | Ingress Adapter | ack или «не авторизован» |
| 3 | Записать SESSION-`<id>`.md через GitHub API | Ingress Adapter | Файл в `inbox/agent/sessions/` |
| 4 | Ответить «Сессия #N запущена» в Telegram | Ingress Adapter | Acknowledgment ≤10с |
| 5 | git pull каждые 15с → файл обнаружен | launchd → Egress Adapter | Trigger сессии |
| 6 | Запустить Claude Code, инжектировать Session Memory | Egress Adapter | Сессия активна |
| 7 | Claude Code выполняет работу | Claude Code | Transcript |
| 8 | Transcript → форматирование → Telegram | Egress Adapter | Ответ пилоту |

---

## Post-MVP (Future)

Eager-pull через GitHub webhook → локальный HTTP endpoint (tailscale funnel). Снижает git-sync latency с avg 7.5с до <1с. Требует ArchGate (tailscale dependency).

---

## Различения

- **Session request ≠ Task (DP.SC.135):** Task имеет `due/template/acceptance/result_location`, SLA ≤1ч, batch. Session — real-time без `due` и `template`. Тест: «есть `due` и `acceptance`?» Нет → session request. Отдельная папка `inbox/agent/sessions/`.
- **Session: light ≠ heavy:** light ≤200 токенов, sync SLA ≤45с. heavy >200 токенов или неопределённая длительность, async + прогресс-нотификации.
