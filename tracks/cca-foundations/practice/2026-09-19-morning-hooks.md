# Practice: Agent SDK hooks (2026-09-19 утро)

Домен D1 · slug `cca-f-d1-agent-hooks` · 5 Q (exam-pattern, не официальные Anthropic).

## Q1. Refund cap $50

Клиент просит refund $120. System prompt: «не превышай $50». Надёжный рычаг?

**Ответ:** PreToolUse `permissionDecision: deny` + reason → HITL. Не prompt-only, не PostToolUse, не silent `[]`.

## Q2. Возврат `{}` из PreToolUse

**Ответ:** нет решения → обычный permission flow (не явный allow).

## Q3. Все Write только в `/sandbox`

**Ответ:** PreToolUse на `Write|Edit` с `updatedInput.file_path` → `/sandbox…` (+ обычно `allow`).

## Q4. Subagent вызвал `issue_refund`

**Ответ:** тот же PreToolUse hook (fire внутри subagent; есть `agent_id`). Не только промпт координатора.

## Q5. После deny по cap

**Ответ:** handoff/HITL со структурированными case-facts (1.4), не blind retry той же суммы.

## Ключевые traps

| Ловушка | Правильно |
|---------|-----------|
| Усилить system prompt | PreToolUse deny |
| `{}` = allow | no decision |
| `updatedInput` top-level | внутри `hookSpecificOutput` |
| Block в PostToolUse | PreToolUse до side-effect |
| Silent `[]` | structured deny / `is_error` |
