# Claude Code в CI/CD: `-p`, structured output и iterative refinement

**Slug:** `cca-f-d3-cicd-print-iterative`  
**Дата:** 2026-09-20 вечер (Europe/Chisinau)  
**Tasks:** 3.5 ✅ · 3.6 ✅  
**Notebook:** [Gemini Notebook](https://notebook.google.com/notebook/b3bb66a0-3daf-4671-bcde-2d02ecd17b0a)

## Суть

- Headless: `claude -p` / `--print` для CI, scripts, pre-commit.
- `--bare` + API key — стабильный runner без host auto-discovery.
- `--output-format json` + `--json-schema` → `structured_output` для automation gates.
- Permissions: `--allowedTools`, `--permission-mode`, `--permission-prompts none`, `--max-turns`.
- Iterative refinement: Plan Mode explore → plan → small diffs → verify → adversarial subagent review.
- Project `CLAUDE.md` в git для команды/CI.

## Дистракторы

Edit-before-explore · monolithic PR context · personal-only CLAUDE.md · unbounded CI loops · `--output-format` без `-p`.

## Артефакты

- Work dir: `/workspace/cca-f-2026-09-20-evening/`
- Practice: [`../../../practice/2026-09-20-evening-cicd-iterative.md`](../../../practice/2026-09-20-evening-cicd-iterative.md)
