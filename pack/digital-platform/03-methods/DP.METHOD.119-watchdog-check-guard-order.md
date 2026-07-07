---
id: DP.METHOD.119
name: Watchdog check guard order (последовательные guard-условия)
kind: method
domain: platform-monitoring
status: active
valid_from: 2026-07-05
source: WP-436 watchdog false-positive root-cause
---

# DP.METHOD.119: Watchdog check guard order

## Описание

Многоступенчатый watchdog выстраивает проверки с явными guard-условиями:
если проверка A прошла успешно, проверка B, чьё предупреждение логически противоречит результату A, должна быть заблокирована.

## IPO

- **Input:** результаты последовательных проверок (A, B, ...) за один прогон
- **Process:** выстроить иерархию проверок с guard-условиями; если check-A PASS, блокировать сигналы B, чей смысл «A должен был FAIL»
- **Output:** согласованный набор предупреждений (без логических противоречий между проверками одного прогона)

## Алгоритм

1. Определить primary check (например: health-probe — «процесс живой?»)
2. Определить secondary checks (например: port-free check — «порт свободен?»)
3. Установить guard: если primary check → PASS, то secondary check, опровергающий primary, → блокируется или помечается `superseded_by_primary`
4. Обратное допустимо: если primary FAIL + port free → валидный сигнал («процесс упал, порт освободился»)

## Пример

```
# Неправильно:
if health_ok:
    raise_alert("port is free!")  # противоречит health_ok

# Правильно:
if health_ok:
    pass  # port-free alert не имеет смысла при живом процессе
elif not health_ok and port_free:
    raise_alert("process died, port released")
```

## Применимость

Многоступенчатые watchdog и healthcheck-системы, где несколько проверок одного объекта могут давать взаимоисключающие сигналы внутри одного прогона.

## Связи

- [DP.D.188](../01-domain-contract/01B-distinctions.md) — Возраст процесса ≠ Зависание (контекст разработки метода)
