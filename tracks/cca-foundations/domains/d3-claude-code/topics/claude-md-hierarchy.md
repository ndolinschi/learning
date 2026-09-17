# CLAUDE.md: иерархия, rules/globs, slash-команды и Plan Mode

- **Дата:** 2026-09-17 вечер (Europe/Chisinau)
- **Домен:** D3 Claude Code Configuration & Workflows (~20%)
- **Slug:** `cca-f-d3-claude-md-hierarchy`
- **Task IDs:** 3.1 ✅ · 3.2 🟡 · 3.3 🟡 · 3.4 🟡 (light) · 3.6 намеренно пропущен
- **Notebook:** [https://notebook.google.com/notebook/24dc1308-686f-4adc-af94-ce7eae420364](https://notebook.google.com/notebook/24dc1308-686f-4adc-af94-ce7eae420364)
- **Артефакты (box):** `/workspace/cca-f-2026-09-17-evening/`

## Ключевые takeaways

1. CLAUDE.md файлы **конкатенируются** (managed → user → project → local); ближе к CWD — позже в контексте.
2. Team standards → project `CLAUDE.md` / `.claude/CLAUDE.md` в git; личное → `~/.claude` / `CLAUDE.local.md`.
3. Модульность: `@imports`, `.claude/rules` + `paths` globs, Skills для процедур (JIT).
4. Plan Mode (`Shift+Tab` / `/plan`) — explore/plan до edit; дистрактор «править сразу».
5. CLAUDE.md ≠ hard security gate → permissions / hooks.

## Practice

См. [`../../practice/2026-09-17-evening-claude-md.md`](../../practice/2026-09-17-evening-claude-md.md).
