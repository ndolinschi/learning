# D5 — Context Management & Reliability (15%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 5.1 Manage conversation context to preserve critical information across long interactions | ✅ | 2026-09-18 вечер | https://notebook.google.com/notebook/91477c4b-dd4c-40b5-8a99-0e63dae0a660 | CORE: case-facts, LITM, compaction, trim/clear tools, caching when-to-use |
| 5.2 Design effective escalation and ambiguity resolution patterns | ✅ | 2026-09-21 вечер | https://notebook.google.com/notebook/6d64abc4-6a01-46f2-83c5-6ffaa924fc76 | явные критерии; sentiment orthogonal; clarify/escalate/documented assumption |
| 5.3 Implement error propagation strategies across multi-agent systems | ✅ | 2026-09-21 вечер | https://notebook.google.com/notebook/6d64abc4-6a01-46f2-83c5-6ffaa924fc76 | structured timeout/error + is_error; never silent [] |
| 5.4 Manage context effectively in large codebase exploration | ⬜ | | | out of scope this drop (cross-link only) |
| 5.5 Design human review workflows and confidence calibration | ✅ | 2026-09-21 вечер | https://notebook.google.com/notebook/6d64abc4-6a01-46f2-83c5-6ffaa924fc76 | HITL tiers; structured packet; confidence ≠ policy gate |
| 5.6 Preserve information provenance and handle uncertainty in multi-source synthesis | ✅ | 2026-09-21 вечер | https://notebook.google.com/notebook/6d64abc4-6a01-46f2-83c5-6ffaa924fc76 | deepen: preserve contradictions + attribution + claim-source |

**Покрытие домена: ~5/6 строк (~83% карты) · exam-ready ~30%**

## Ключевые сценарии / темы

- Case-facts / durable facts block (не «просто больший context window»)  
- Escalation: явные критерии + few-shot; ambiguity triad  
- Error propagation: classified is_error / structured timeout to coordinator  
- HITL: AUTO/NOTIFY/APPROVE/BLOCK; structured handoff; audit trail  
- Confidence calibration: deterministic gates > self-confidence  
- Provenance: conflicting claims with attribution  
- 5.4 large codebase — ещё не drill  

## Тема вечера

[`topics/escalation-hitl.md`](topics/escalation-hitl.md)