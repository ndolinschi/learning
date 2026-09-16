# D2 — Tool Design & MCP Integration (18%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 2.1 Design effective tool interfaces with clear descriptions and boundaries | ⬜ | | | |
| 2.2 Implement structured error responses for MCP tools (`isError`, retryable vs not) | ⬜ | | | |
| 2.3 Distribute tools appropriately across agents and configure `tool_choice` | ⬜ | | | |
| 2.4 Integrate MCP servers into Claude Code and agent workflows (`.mcp.json`, resources/prompts) | ⬜ | | | |
| 2.5 Select and apply built-in tools (Read, Write, Edit, Bash, Grep, Glob) effectively | ⬜ | | | |

**Покрытие домена: 0/5 (0%)**

## Ключевые сценарии / темы

- Customer Support: 4–5 tool registry, чёткие границы get_customer / lookup_order / refund  
- Developer Productivity: trade-offs tool surface, built-in vs MCP  
- PreToolUse refund cap / policy interception (пересечение с D1.5)  
- Scoped tools для synthesis vs полный web-search  
- MCP project vs user scope, credential env expansion
