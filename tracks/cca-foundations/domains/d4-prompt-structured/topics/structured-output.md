# Структурированный вывод: JSON Schema, tool_choice, few-shot и validation/retry

- **Дата:** 2026-09-18 утро (Europe/Chisinau)
- **Домен:** D4 Prompt Engineering & Structured Output (~20%)
- **Slug:** `cca-f-d4-structured-output`
- **Task IDs:** 4.1 🟡 · 4.2 ✅ · 4.3 ✅ · 4.4 ✅ · 4.5/4.6 отложены
- **Notebook:** (NotebookLM — отдельный агент)
- **Артефакты (box):** `/workspace/cca-f-2026-09-18-morning/`

## Ключевые takeaways

1. Exam-core: tool с `input_schema` + `tool_choice: {"type":"tool","name":…}` + escapes + schema→semantic→targeted retry.
2. Newer official: `output_config.format` (JSON outputs) и `strict: true` — production/[VS]; semantic validation всё равно нужна.
3. SkillCert CLI `--json-schema` «100%» — не gospel.
4. Few-shot: 3–5 сбалансированных; не замена схемы; ловушка label imbalance.
5. Explicit criteria &gt; «be conservative»; programmatic enforcement &gt; prompt-only для hard constraints.

## Practice

См. [`../../../practice/2026-09-18-morning.md`](../../../practice/2026-09-18-morning.md).
