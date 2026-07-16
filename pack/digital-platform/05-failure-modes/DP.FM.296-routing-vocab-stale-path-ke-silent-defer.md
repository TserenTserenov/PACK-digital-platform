---
id: DP.FM.296
name: "Некорректный путь в routing-vocab молча блокирует KE — defer вместо accept"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-10
source: "DS-ai-systems commit d073ec2 + peer-session 2026-07-10-14-apply-captures-reject-defer"
related:
  see_also:
    - "DP.FM.260: file-path resolution error masked as logic error (смежно — другой слой)"
tags: [knowledge-extraction, routing-vocab, path-resolution, ke-process, defer-false-positive]
---

# DP.FM.296 — Некорректный путь в routing-vocab молча блокирует KE

## Паттерн

Некорректный путь к директории Pack в `routing-vocab.md` → агент не находит директорию → кандидаты систематически попадают в `defer` с пометкой «нет нужного Pack» → **ложный defer**, не реальное отсутствие Pack.

## Инцидент

PACK-personal: путь `pack/personal/` (несуществующий) вместо `pack/personal-development/` (реальный). Пробел существовал >1 месяца — 8+ кандидатов KE систематически уходили в defer.

## Диагностика

**Тест:** «3+ defer-кандидата с одинаковым "предполагаемым Pack" за короткий период?» → проверить путь в routing-vocab против реальных директорий:

```bash
ls ~/IWE/PACK-*/pack/*/  # фактические пути
grep "pack/" routing-vocab.md  # объявленные пути
```

Дефект **не диагностируется** в обычном apply-captures — defer выглядит как «нет нужного Pack», а не как «путь битый».

## Fix

Исправить путь в `routing-vocab.md` → перепроверить накопленные defer-кандидаты по этому домену.

## Правило

**Week Close:** 2-минутная проверка путей routing-vocab против реальных директорий — превентивная проверка.

**Любое касание routing-vocab.md** (добавление или правка записи) = валидация всех записей файла, не только изменённой строки. Протухший путь к соседнему Pack обнаруживается только при касании соседней записи (случай регистрации PACK-rhetoric, 2026-07-10):

```bash
grep -oE '~/IWE/PACK-[^/ ]+' routing-vocab.md | while read path; do
  [ -d "${path/#\~/$HOME}" ] || echo "STALE: $path"
done
```

Мета-паттерн decay конфигурационных путей: [DP.FM.016].
