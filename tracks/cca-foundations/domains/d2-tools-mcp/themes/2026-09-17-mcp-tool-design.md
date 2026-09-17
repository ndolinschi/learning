# MCP и дизайн инструментов: схемы, границы, isError, tool_choice

- **Трек:** CCA-F Foundations
- **Домен:** D2 Tool Design & MCP Integration (~18%)
- **Дата:** 2026-09-17 (утро, Europe/Chisinau)
- **Статус:** ✅ (закрывает 2.1, 2.2, 2.3; частично 2.4; 2.5 — нет)
- **Notebook:** (создаётся отдельно; URL в meta.json позже)
- **Practice:** [`../../../practice/2026-09-17-mcp-tools.md`](../../../practice/2026-09-17-mcp-tools.md)
- **Research sibling:** [`2026-09-17-mcp-tool-design-research.md`](2026-09-17-mcp-tool-design-research.md)

## Кратко

Контракт tools: подробные descriptions + JSON Schema + границы read/write. Ошибки — через `tool_result` с `is_error: true` и категориями retryable/not. Управление вызовами — `tool_choice` и scoped/`tools[]` (в т.ч. MCPToolset allowlist/denylist). MCP в Claude Code — `.mcp.json` (project/user/local); в Messages API — connector `mcp_servers` + `mcp_toolset`.

## Ключевые идеи

1. Description — primary selector; vague description = wrong tool calls.
2. `is_error` + instructive content; silent `[]` на timeout — антипаттерн.
3. `tool_choice`: auto / any / tool / none; `disable_parallel_tool_use`.
4. Distribute tools по ролям; denylist destructive MCP tools.
5. Project `.mcp.json` для команды; секреты через `${ENV}`, не plaintext.

## Task IDs

| ID | Эффект этого дропа |
|----|--------------------|
| 2.1 tool interfaces / boundaries | ✅ |
| 2.2 structured MCP/tool errors isError | ✅ |
| 2.3 tool_choice / distribute tools | ✅ |
| 2.4 MCP in Claude Code `.mcp.json` | 🟡 (intro scopes + connector; resources/prompts deep — нет) |
| 2.5 built-in Claude Code tools | ⬜ |

## Материалы

- Полный research / doc PDF: `/workspace/cca-f-2026-09-17-morning/` на Learning Bot box
- Источники: platform.claude.com (tool use, define/handle tools, MCP connector); code.claude.com MCP; SkillCertPro §2.1–2.6
