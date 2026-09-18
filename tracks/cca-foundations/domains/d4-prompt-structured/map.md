# D4 — Prompt Engineering & Structured Output (20%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 4.1 Design prompts with explicit criteria to improve precision and reduce false positives | 🟡 | 2026-09-18 утро | (NotebookLM pending) | light: explicit criteria vs «be conservative» |
| 4.2 Apply few-shot prompting to improve output consistency and quality | ✅ | 2026-09-18 утро | pending | 3–5 balanced; label imbalance trap |
| 4.3 Enforce structured output using tool use and JSON schemas | ✅ | 2026-09-18 утро | pending | CORE: forced tool_choice + schema; + output_config/strict [VS] |
| 4.4 Implement validation and targeted retry loops (schema vs semantic) | ✅ | 2026-09-18 утро | pending | schema→semantic→targeted repair; programmatic gates |
| 4.5 Design efficient batch processing strategies (Message Batches) | ⬜ | | | отложено намеренно |
| 4.6 Apply multi-pass / independent review patterns | ⬜ | | | отложено намеренно |

**Покрытие домена: ~3.5/6 строк (~58% карты) · exam-ready ~20%**

## Ключевые сценарии / темы

- Structured Data Extraction: `input_schema` + forced `tool_choice` + nullable/«unclear» + semantic validate→retry  
- Newer API: `output_config.format` JSON outputs + `strict: true` на tools  
- Explicit review criteria vs «be conservative»  
- Few-shot для escalation / ambiguous cases (balanced)  
- Batch API / multi-pass — ещё не drill  

## Тема утра

[`topics/structured-output.md`](topics/structured-output.md)
