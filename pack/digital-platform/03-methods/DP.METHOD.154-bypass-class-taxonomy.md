---
id: DP.METHOD.154
type: method
domain: PACK-digital-platform
status: draft
summary: "Bypass-class taxonomy: для каждого класса обхода security-гарда — отдельный regression-тест, подтверждающий, что нужный слой его ловит. Пустой класс = непокрытый вектор атаки."
created: 2026-06-26
valid_from: 2026-06-26
version: v1.0
source: "peer-session 2026-06-26-26-wp436-claude-writer, turn 1 + turn 2; WP-436"
related:
  see_also: [DP.D.222, DP.FM.237, DP.FM.238]
  note: "расширяет паттерн mutation-testing (одно намеренное нарушение → guard падает) добавлением таксономии классов"
---

# DP.METHOD.154: Набор red-team тестов обхода (bypass-class taxonomy)

## Назначение

Метод верификации полноты security-гарда: для каждого класса обхода указать ответственный слой-ловец и написать regression-тест, доказывающий что этот слой перехватывает именно свой класс.

## Проблема

Одиночный mutation-test («гард падает при намеренном нарушении») подтверждает только базовый случай. Реальные атаки используют разные векторы: прямой обход, косвенный через переменные/генерацию, обход хука, поддельное enforcement-поле. Без явной таксономии — неизвестно, покрыт ли каждый вектор.

## Метод

### Шаг 1: Заполнить таблицу bypass-классов

| Класс | Описание | Ответственный слой | Тест написан? |
|-------|----------|-------------------|---------------|
| **Direct** | Прямой raw-вызов запрещённой операции | pre-commit hook / delta-aware lint | ☐ |
| **Indirect** | Обход через переменную/генерацию | семантический слой (граф вызовов / static analysis) | ☐ |
| **Hook-bypass** | `--no-verify` или отключение хука локально | серверный CI (REQUIRED_STATUS_CHECKS) | ☐ |
| **Field-spoof** | Поддельное enforcement-поле в конфиге | provenance-проверка / D4-гард | ☐ |

Расширяется по мере обнаружения новых векторов.

### Шаг 2: Написать regression-тест на каждый класс

```python
# Тест Direct bypass:
def test_direct_bypass_blocked_by_precommit():
    result = subprocess.run(["git", "commit", ...add_bypass_file...], ...)
    assert result.returncode != 0  # pre-commit блокирует

# Тест Hook-bypass:
def test_hook_bypass_blocked_by_ci():
    result = trigger_ci_with_no_verify_commit(...)
    assert ci_check_status(result) == "FAILED"  # CI ловит
```

### Шаг 3: Проверить полноту

**Тест полноты:** «Есть ли класс обхода без соответствующего теста-ловца?» Да → непокрытый вектор → дыра.

Пустой класс = обнаруженная уязвимость. Требует либо дизайна нового слоя, либо явного решения «принять риск» с документацией.

## Связи

- Расширяет: паттерн mutation-testing (добавляет таксономию классов + мэппинг на слои)
- Distinction: DP.D.222 (non-blocking audit ≠ protection layer)
- FM: DP.FM.237 (field-spoof через внерепо стейт), DP.FM.238 (self-referential exemption)
- Применяется: в контурах defence-in-depth при наличии многослойной защиты
