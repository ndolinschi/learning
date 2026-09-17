# Research: MCP и дизайн инструментов (CCA-F D2)

**Тема:** MCP и дизайн инструментов: схемы, границы, isError, tool_choice  
**Slug:** `cca-f-d2-mcp-tool-design`  
**Дата:** 2026-09-17 утро (Europe/Chisinau)  
**Домен:** D2 Tool Design & MCP Integration (~18%)  
**Метод:** официальные docs Anthropic / MCP + SkillCertPro §2.1–2.6 (offline). Claude.ai не использовался (риск spend limit).

## Источники (цитаты)

1. [Tool use overview](https://docs.anthropic.com/en/docs/build-with-claude/tool-use/overview) / [platform.claude.com tool-use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview) — client vs server tools, цикл `tool_use` → `tool_result`, `tool_choice`.
2. [Define tools](https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools) — `name`, `description`, `input_schema`, `input_examples`, `strict`, `tool_choice`: `auto` | `any` | `tool` | `none`, `disable_parallel_tool_use`.
3. [Handle tool calls](https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls) — формат `tool_result`, **`is_error: true`**, порядок блоков (result FIRST), instructive error messages.
4. [MCP connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) — beta `mcp-client-2025-11-20`, `mcp_servers` + `mcp_toolset`, allowlist/denylist, `mcp_tool_use` / `mcp_tool_result` (+ `is_error`), только remote HTTP/SSE (не local STDIO через connector).
5. [Claude Code MCP](https://code.claude.com/docs/en/mcp) / [mcp-quickstart](https://code.claude.com/docs/en/mcp-quickstart) — scopes: `local` / `project` (`.mcp.json`) / `user`; `stdio` vs `http`/`streamable-http`; `${VAR}` expansion.
6. MCP spec Tools (modelcontextprotocol, 2026-07-28) — `tools/list`, `tools/call`, structured results, error handling на уровне протокола.
7. SkillCertPro CCA-F Master Cheat Sheet §2.1–2.6 (`/workspace/cca-f-pdf-analysis/sections/`) — Host/Client/Server, границы tools, schema best practices, категории ошибок retryable vs not.

## Ключевые факты для экзамена

### A. Интерфейсы tools (task 2.1)

- Описание — **главный селектор**: что делает, когда использовать / не использовать, параметры, ограничения (Anthropic: 3–4+ предложения).
- Схема: `required`, `enum`, описания полей; опционально `input_examples`, `strict: true` для гарантии schema-valid inputs.
- Границы: single-purpose (или осознанная группировка через `action`), read vs write раздельно, валидация входов, лимиты размера ответа, idempotency для retries.
- Customer Support registry: отдельные `get_customer`, `lookup_order`, `issue_refund` с чёткими precondition в description — не один `manage_support`.

### B. Структурированные ошибки (task 2.2)

- Client tools: при сбое вернуть `tool_result` с `"is_error": true` и **полезным** текстом (что сломалось + что попробовать).
- Антипаттерн: тихий `[]` / пустой success при timeout → координатор синтезирует ложь.
- Категории (SkillCert + практика): `rate_limit`/`timeout` → retryable; `invalid_input`/`unauthorized`/`not_found` → не retry без смены входа; `internal_error` → maybe.
- MCP connector result block тоже несёт `is_error`.
- Untrusted tool content держать внутри `tool_result`, не в system prompt (prompt injection).

### C. tool_choice и распределение tools (task 2.3)

| `tool_choice` | Поведение |
|---------------|-----------|
| `auto` (default) | Модель решает: tool или prose |
| `any` | Обязан вызвать один из tools |
| `tool` + `name` | Принудительно конкретный tool |
| `none` | Запрет tools |

- `disable_parallel_tool_use: true` — максимум один вызов за ход.
- Scoped tools для субагентов / synthesis (least privilege) — пересечение с D1, но рычаг D2: **какие** tools в `tools[]`.
- MCPToolset allowlist/denylist — programmatic gate вместо «не вызывай delete» в промпте.
- Developer Productivity: built-in (Read/Write/Bash…) vs MCP vs custom API tools — выбирать по surface и trust boundary.

### D. MCP в Claude Code — intro (task 2.4 🟡)

- Project scope: `.mcp.json` в корне репо (commit для команды).
- User/local: `~/.claude.json`.
- Типы: `stdio` (`command`/`args`/`env`), `http` / alias `streamable-http` (`url`, `headers`).
- Env expansion `${API_KEY}` / `${VAR:-default}` — секреты не хардкодить в репо.
- Messages API MCP connector ≠ Claude Code `.mcp.json`: connector только remote URL + beta header; Claude Code умеет local stdio.

## Сценарии

1. **Customer Support tool registry** — 4–5 узких tools + refund write отдельно + PreToolUse cap (упоминание; hooks — D1.5/позже).
2. **Developer Productivity** — GitHub MCP vs built-in Bash/`gh`; denylist destructive tools для junior agent.
3. **Multi-agent research** — researchers с search tools; synthesizer только verify/read — distribute tools.

## Что сознательно не углубляли

- Built-in Claude Code tools catalog (task 2.5) — отдельный drop.
- OAuth 2.1/PKCE детали MCP auth — только token field у connector.
- SSE vs Streamable HTTP trade-offs — поверхностно.
- Pre/PostToolUse hooks drill — backlog.

## Coverage estimate (консервативно)

- Task map: 2.1 ✅, 2.2 ✅, 2.3 ✅, 2.4 🟡, 2.5 ⬜ → ~70% строк карты D2.
- Exam-ready D2: **~22%** (одна тема + 5 practice Q, без hands-on MCP server).
- Overall: 0.27·22 + 0.18·22 + 0.15·2 ≈ **~10%**.
