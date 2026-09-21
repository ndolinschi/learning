# Message Batches и multi-pass: пакетная обработка и независимое ревью

**Slug:** `cca-f-d4-batches-multipass`  
**Дата:** 2026-09-21 утро (Europe/Chisinau)  
**Tasks:** 4.5 ✅ · 4.6 ✅  
**Notebook:** [Gemini Notebook](https://notebook.google.com/notebook/a8ad563f-2ebd-4832-8119-991f81bbc998)

## Суть

- Message Batches: async create → poll → JSONL results; уникальный `custom_id`.
- Только **non-blocking** (bulk eval/extraction/moderation); не chat/HITL/blocking pre-merge.
- Лимиты: 100k|256MB, 24h окно, 29 дней results; ~50% cost (без pricing math).
- Multi-pass: generate → независимый review-pass (отдельный call/agent + rubric) → merge.
- File review: per-file + integration; не monolithic dump.
- Self-critique в том же контексте ≠ independent review.

## Дистракторы

Batches для chat · Batches для blocking CI · self-check как independent · sync for-loop на bulk · порядок JSONL без `custom_id`.

## Артефакты

- Work dir: `/workspace/cca-f-2026-09-21-morning/`
- Practice: [`../../../practice/2026-09-21-morning-batches-multipass.md`](../../../practice/2026-09-21-morning-batches-multipass.md)
