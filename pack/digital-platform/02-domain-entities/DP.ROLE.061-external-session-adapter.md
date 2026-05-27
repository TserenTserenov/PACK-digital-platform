---
id: DP.ROLE.061
name: External Session Adapter
name_ru: Адаптер внешних сессий
name_en: External Session Adapter
type: role
status: draft
layer: L4-Platform
summary: "Мост между внешним каналом (Telegram) и локальным исполнителем (Claude Code в VS Code). Не выполняет задачу — только маршрутизирует запрос и ответ между каналами. Две sub-responsibility: Ingress (cloud) и Egress (local)."
owner_role: R14 Заказчик (пилот) — как потребитель; DP.ROLE.045 Диспетчер — как смежная роль в inbox
created: 2026-05-27
updated: 2026-05-27
related:
  realizes: DP.SC.162           # External Session Request
  uses:
    - DP.SC.135                 # Agent Inbox — смежный, не путать (разные папки)
    - DP.SC.161                 # Session Memory Injector — вызывается при старте сессии
  see_also:
    - DP.ROLE.045               # Диспетчер (batch-режим, отдельный контракт)
    - DP.ROLE.059               # Маршрутизатор (routing-layer)
wp: WP-358
---

# [DP.ROLE.061] Адаптер внешних сессий

## Назначение

Мост между внешним каналом (Telegram) и локальным исполнителем (Claude Code в VS Code).

**Ключевые принципы:**
- Не выполняет задачу — только маршрутизирует запрос и ответ между каналами
- Распределённая роль: два deployment-endpoint с разными auth-границами
- Один logical owner — оба endpoint реализуют контракт DP.SC.162

---

## Sub-responsibilities

### Ingress Adapter (облачный, `aist-bot`, cloud-side)

**Кто:** компонент Telegram-бота (@aist_pilot_bot), деплоится в cloud.

**Обязанности:**
1. Принять команду `/claude [--private] <текст>` из разрешённого чата
2. Авторизовать: `tg_chat_id ∈ allowed_list` (config-based, MVP)
3. Записать `inbox/agent/sessions/SESSION-<id>.md` через GitHub API с frontmatter:
   - `session_id`, `tg_chat_id`, `message_id` (idempotency key), `text`, `private`, `status: pending`
4. Отправить acknowledgment «Сессия #N запущена» в Telegram ≤10с

**Полномочия:** read `allowed_list`, GitHub API write к `inbox/agent/sessions/`, `send_telegram_message`.

**НЕ делает:** не запускает сессию, не ждёт результата.

---

### Egress Adapter (локальный, `iwe-agent-dispatcher.py --mode session`, local-side)

**Кто:** расширение `iwe-agent-dispatcher.py`, деплоится на машине пилота.

**Обязанности:**
1. Обнаружить SESSION-файл с `status: pending` (launchd poll каждые 15с)
2. Установить composite key idempotency: проверить `(tg_chat_id, message_id)` по SQLite (`~/.iwe/sessions.db`)
3. Вызвать Session Memory Injector (DP.SC.161) для pre-flight обогащения контекста
4. Запустить Claude Code в VS Code с SESSION-файлом как входом
5. Heartbeat: ping в SQLite каждые 30с (`session_heartbeat`); miss >90с → `failed`
6. Получить transcript сессии
7. Форматировать ответ и отправить в Telegram через WP-320 (`send_telegram_message`)
8. Обновить SESSION-файл: `status: completed/failed/cancelled`, `completed_at`
9. Записать transcript в `inbox/agent/sessions/SESSION-<id>-transcript.md` (если не `--private`)

**Полномочия:** read/write `inbox/agent/sessions/`, read/write `~/.iwe/sessions.db`, запуск Claude Code CLI, использовать `send_telegram_message`.

**НЕ делает:** не выбирает содержание ответа — это задача Claude Code.

---

## Инварианты роли

1. **Идемпотентность через composite key.** `(tg_chat_id, message_id)` проверяется до создания SESSION-файла (Ingress) и до запуска сессии (Egress). Повторный запрос с тем же key — no-op с возвратом текущего статуса.

2. **Atomicity acknowledgment.** Ingress НЕ возвращает ответ до записи SESSION-файла. Если GitHub API упал → пользователь получает «Не удалось создать сессию», не acknowledgment.

3. **Heartbeat ≠ session signal.** Heartbeat Egress — self-healthcheck диспетчера (ping в SQLite), не сигнал от активной сессии. Длительный heavy-запрос не должен ложно тригеррить `failed` из-за отсутствия activity.

4. **Private audit trail.** Для `--private` сессий: transcript хранится ТОЛЬКО в `~/.iwe/sessions.db` (никогда не в git). SESSION-файл содержит только метаданные.

5. **Graceful degradation.** VS Code не открыт / диспетчер не запущен → Egress обнаруживает timeout (miss heartbeat на этапе spin-up) → TG «Среда не запущена». Тихий fail запрещён.

---

## Масштабирование (Multi-pilot future, Q4 WP-358)

| Компонент | MVP | Multi-pilot |
|-----------|-----|------------|
| Ingress Adapter | один экземпляр (один пилот) | горизонтальное масштабирование (cloud stateless) |
| Egress Adapter | привязан к машине пилота | per-pilot инстанс на каждой машине |
| SESSION-файлы | один репозиторий | per-pilot namespace или multi-tenant repo |
| Authorization | tg_chat_id allowlist | Ory OAuth2 (пост-MVP, ArchGate) |

---

## Отличие от DP.ROLE.045 (Диспетчер)

| Аспект | DP.ROLE.045 Диспетчер | DP.ROLE.061 Адаптер |
|--------|----------------------|---------------------|
| Контракт входа | Task с `due/template/acceptance` | Session request (real-time) |
| Папка | `inbox/agent/tasks/` | `inbox/agent/sessions/` |
| SLA | ≤1ч (batch) | acknowledgment ≤10с |
| Failure mode | retry с exponential backoff | TG alert + graceful fail |
| Trigger | cron/systemd | launchd poll 15с |
| Deployment | headless, без VS Code | VS Code primary host |

---

## Kind и Owner

- **Kind:** Bridge Role (соединяет два runtime — cloud и local)
- **Owner Role в надсистеме:** Заказчик (R14) как потребитель сервиса; Infrastructure как operator
