# D2 — Tool Design & MCP Integration (18%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 2.1 Design effective tool interfaces with clear descriptions and boundaries | ✅ | 2026-09-17 | (pending NotebookLM) | descriptions, schema, read/write, Support registry |
| 2.2 Implement structured error responses for MCP tools (`isError`, retryable vs not) | ✅ | 2026-09-17 | (pending) | `is_error` / instructive errors / categories |
| 2.3 Distribute tools appropriately across agents and configure `tool_choice` | ✅ | 2026-09-17 | (pending) | auto/any/tool/none, scoped tools, allowlist |
| 2.4 Integrate MCP servers into Claude Code and agent workflows (`.mcp.json`, resources/prompts) | ✅ | 2026-09-19 | (evening NotebookLM pending) | scopes + resources/prompts + OAuth/env/headersHelper |
| 2.5 Select and apply built-in tools (Read, Write, Edit, Bash, Grep, Glob) effectively | ✅ | 2026-09-19 | (evening NotebookLM pending) | composition, Edit vs Write vs Bash, subagent scopes |

**Покрытие домена: 5/5 строк карты (~100%); exam-ready ~29%**

## Ключевые сценарии / темы

- Customer Support: 4–5 tool registry, чёткие границы get_customer / lookup_order / refund  
- Developer Productivity: композиция Glob/Grep→Read→Edit; built-in vs MCP vs Skills  
- Scoped tools для review-subagent vs полный write surface  
- MCP project vs user scope, credential env expansion, OAuth scopes, resources `@`  
- PreToolUse refund cap / policy (пересечение с D1.5) — covered via morning hooks  

## Темы

- [2026-09-17 MCP и дизайн инструментов](themes/2026-09-17-mcp-tool-design.md)
- [2026-09-19 Built-in tools + MCP deep](topics/builtin-mcp-deep.md)
