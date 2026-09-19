# Built-in Claude Code tools + MCP deep

**Slug:** `cca-f-d2-builtin-mcp-deep`  
**Дата:** 2026-09-19 вечер (Europe/Chisinau)  
**Домен:** D2 Tool Design & MCP Integration  
**Tasks:** 2.5 ✅ · 2.4 ✅ (resources/prompts + auth)

## Одной строкой

Композиция Read/Write/Edit/Bash/Grep/Glob + scoped subagents; MCP resources/prompts/scopes/auth (`mcp__server__tool`, `${VAR}`, OAuth) — без повторения deep hooks (D1 утро).

## Ключевые рычаги

- Glob/Grep → Read → Edit (не Bash-sed); Write = новый/полная перезапись
- Review-subagent: `[Read, Grep, Glob, Bash]` без Write/Edit
- Path allow: `Edit(path)` покрывает Write
- Resources `@` vs tools vs prompts slash
- Project `.mcp.json` + env/OAuth; секреты не в CLAUDE.md
- Имя: `mcp__github__list_issues`

## Артефакты

- Work dir: `/workspace/cca-f-2026-09-19-evening/`
- Описательный PDF: `builtin-mcp-opisatelnyj-doc.pdf`
- Practice: [`../../practice/2026-09-19-evening-builtin-mcp.md`](../../practice/2026-09-19-evening-builtin-mcp.md)

## Coverage

D2 exam-ready ~22%→~29%; карта 5/5 ~100%; overall ~23%→~24%.
