# Practice: Message Batches + multi-pass (2026-09-21 утро)

Домен D4 · tasks 4.5 + 4.6 · 5 scenario Q

## Q1 — Ночные 5k tickets
**Правильный рычаг:** Message Batches + `custom_id` + structured extraction.  
**Дистракторы:** sync UI-бот; один гигантский prompt; `stream: true` в batch.

## Q2 — Blocking pre-merge security gate
**Правильный рычаг:** Sync Messages или `claude -p` + JSON schema в CI job.  
**Дистракторы:** Batches; ждать 24h; self-critique как «independent».

## Q3 — PR на 60 файлов
**Правильный рычаг:** Multi-pass per-file + integration review в свежем контексте.  
**Дистракторы:** монолитный diff; только self-critique; Batches обязателен для IDE.

## Q4 — JSONL порядок / errors
**Правильный рычаг:** матч по `custom_id`; per-item error handling; один fail ≠ весь batch.  
**Дистракторы:** индекс i↔i; drop custom_id; fail-all.

## Q5 — Independent semantic review
**Правильный рычаг:** отдельный Messages-вызов/агент: JSON + документ + rubric.  
**Дистракторы:** self-critique в том же turn; temperature; Batches для одного interactive doc.

## Ключевые факты
- Batches = non-blocking only; ~50% cost; 24h / 29d / 100k|256MB
- `custom_id` обязателен для reconcile
- Independent review ≠ same-context self-check
