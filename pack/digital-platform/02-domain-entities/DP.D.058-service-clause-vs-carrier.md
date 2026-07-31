---
id: DP.D.058
name: "Service Clause (Обещание) ≠ Carrier (Носитель реализации)"
type: distinction
status: active
valid_from: 2026-05-12
source: "WP-305 A-lite revision, ADR-IWE-016 supersedes ADR-IWE-015"
---

# DP.D.058: Service Clause (Обещание) ≠ Carrier (Носитель реализации)

| Аспект | Service Clause | Carrier |
|--------|---------------|---------|
| **Суть** | Что гарантируется потребителю | Кто физически выполняет |
| **Стабильность** | Высокая (меняется только при смене обещания) | Низкая (меняется при архитектурной ревизии) |
| **Артефакт** | `DP.SC.NNN` в 08-service-clauses/ | Поле carrier в `DP.ROLE.NNN` |
| **Тест** | «Что гарантируется потребителю?» | «Кто / какой модуль / Worker?» |

**Правило:** Архитектурная ревизия меняет carrier в ROLE.NNN, но **не SC.NNN**. Если меняется только «как» (отдельный CF Worker → модуль внутри другого Worker), а не «что гарантируется» — SC не трогаем.

**Пример (WP-305 A-lite):** DP.SC.130 (OAuth gateway обещание) не изменилось. Изменён carrier: отдельный CF Worker → модуль `gateway-mcp/src/oauth/` + CF custom domain alias. ADR-IWE-016 supersedes ADR-IWE-015.

**Тест:** «Если carrier изменился — изменилось ли обещание потребителю?» Нет → SC стабилен, carrier обновляем в ROLE.

> Связь: DP.D.039 (реализация без обещания = ошибка). IntegrationGate §1: SC создаётся до реализации, carrier определяется на шаге §4.
