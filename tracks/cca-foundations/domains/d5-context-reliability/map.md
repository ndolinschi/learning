# D5 — Context Management & Reliability (15%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 5.1 Manage conversation context to preserve critical information across long interactions | ✅ | 2026-09-18 вечер | https://notebook.google.com/notebook/91477c4b-dd4c-40b5-8a99-0e63dae0a660 | CORE: case-facts, LITM, compaction, trim/clear tools, caching when-to-use |
| 5.2 Design effective escalation and ambiguity resolution patterns | ⬜ | | | related mention only (explicit criteria); deep later |
| 5.3 Implement error propagation strategies across multi-agent systems | ⬜ | | | out of scope this drop |
| 5.4 Manage context effectively in large codebase exploration | ⬜ | | | out of scope this drop |
| 5.5 Design human review workflows and confidence calibration | ⬜ | | | out of scope this drop |
| 5.6 Preserve information provenance and handle uncertainty in multi-source synthesis | 🟡 | 2026-09-18 вечер | https://notebook.google.com/notebook/91477c4b-dd4c-40b5-8a99-0e63dae0a660 | light: conflicting claims + attribution |

**Покрытие домена: ~1.5/6 строк (~25% карты) · exam-ready ~19%**

## Ключевые сценарии / темы

- Case-facts / durable facts block (не «просто больший context window»)  
- Lost-in-the-middle; pin ключевых фактов; trimming / clearing tool results  
- Compaction API (`compact_20260112`, min 50k, custom instructions, pass block back)  
- Prompt caching: stable prefix + ephemeral (~5 мин)  
- Escalation: явные критерии + few-shot (related; deep 5.2 later)  
- Provenance light: conflicting claims с attribution  
- 5.3–5.5 — ещё не drill  

## Тема вечера

[`topics/context-management.md`](topics/context-management.md)
