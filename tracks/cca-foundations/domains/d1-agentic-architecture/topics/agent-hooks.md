# Хуки Agent SDK: Pre/PostToolUse, enforcement vs prompt-only

**Slug:** `cca-f-d1-agent-hooks`  
**Дата:** 2026-09-19 утро (Europe/Chisinau)  
**Домен:** D1 · tasks **1.5** ✅, **1.4** ✅ (усиление)  
**Notebook:** https://notebook.google.com/notebook/60e1ccc2-f487-4a7c-bfa7-3b9e94bb6252

## Ядро

1. **PreToolUse** — до tool: `permissionDecision` allow/deny/ask/defer; `updatedInput`; `permissionDecisionReason`.
2. **PostToolUse** — после: audit, `additionalContext`, `updatedToolOutput` (tool уже выполнен).
3. **Enforcement vs prompt-only** — hard policy (refund cap, `.env`, sandbox path) → hook, не «усилить system prompt».
4. **`{}`** = no decision; priority **deny > defer > ask > allow**.
5. **Silent `[]` / empty success** — запрещены при policy fail; нужен structured deny / `is_error`.
6. **Subagent** — те же hooks + `agent_id`/`agent_type`; handoff после deny (1.4).

## Официальные источники

- https://code.claude.com/docs/en/agent-sdk/hooks
- https://code.claude.com/docs/en/hooks

## Артефакты drop

- Work dir: `/workspace/cca-f-2026-09-19-morning/`
- Описательный PDF + presentation PDF + NotebookLM studio
- Practice: [`../../practice/2026-09-19-morning-hooks.md`](../../practice/2026-09-19-morning-hooks.md)
