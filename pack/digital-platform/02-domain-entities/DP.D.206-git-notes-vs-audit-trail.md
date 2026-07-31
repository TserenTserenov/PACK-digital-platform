---
id: DP.D.206
name: "git notes ≠ видимый аудиторский след"
name_ru: "git notes не являются видимым аудиторским следом"
type: distinction
domain: digital-platform
status: active
valid_from: 2026-06-20
schema_version: 1
source: "peer-session 2026-06-20-39, report.md; разбор альтернатив для инцидента mis-attribution"
---

# DP.D.206 — git notes ≠ видимый аудиторский след

| Аспект | git notes | IWE-native audit trail |
|--------|-----------|------------------------|
| Видимость в `git log --oneline` | Нет | Да (bug-файл grep-доступен) |
| GitHub UI по умолчанию | Нет | Да (файл в репо) |
| Fetch клиентами | Требует явного `fetch refs/notes/commits` | Автоматически с репо |
| Discoverable через год | Маловероятно | Да (grep, search) |
| Случайное удаление | Без предупреждения | Git history сохраняет |

**Инвариант:** человек, наткнувшийся на коммит через год, ноту с высокой вероятностью не увидит без специальных флагов.

**Тест:** «Будет ли запись видна другому человеку без флагов `--notes` / `fetch refs/notes/commits`?»
- Нет → git notes, аудиторский след ненадёжный
- Да → видимый trail (bug-файл, errata-секция, commit body)

**IWE-native решение для инцидент-записей:**
1. `inbox/bugs/bug-YYYY-MM-DD-<тема>.md` — видимый, индексированный, grep-доступный
2. Errata-секция в `archive/wp-contexts/WP-N.md` — привязана к контексту РП
3. Commit body (не message строка) — виден в `git log`, GitHub, большинстве клиентов

**Область применения:** при выборе метода ведения аудиторского следа для инцидентов, mis-attribution, post-mortem.

## Связи

- IWE bug reporting convention: `inbox/bugs/bug-*.md` как IWE-native альтернатива
- peer-session 2026-06-20-39: рассматривался `git notes add` как audit след для mis-attribution — отклонён именно из-за данного различения
