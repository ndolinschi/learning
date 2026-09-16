# CCA-F: приоритетный backlog

Приоритет = **вес домена × вероятность появления** (по research 2026-09-15: scenario density + независимые гайды).  
Не слепая ротация тем — только исследованные рычаги экзамена.

## P0 — максимальный рычаг

1. **D1 (27%, very high)** — agent vs workflow; циклы и `stop_reason`; coordinator/subagent; явная передача состояния; persistence; escalation; timeouts; bounded retries; human-in-the-loop.
2. **D3 (20%, very high)** — иерархия CLAUDE.md; Plan Mode; Skills; `.claude/rules` globs; targeting контекста; permissions; scoped subagents; границы CI/CD review.
3. **D2 (18%, very high по плотности сценариев)** — описания/схемы tools; MCP servers/resources/prompts; custom vs built-in vs MCP vs Skills; Pre/PostToolUse hooks; изоляция ошибок.

## P1 — высокий

4. **D4 (20%, high)** — system prompts; structured output через tools; schema vs prose; forced `tool_choice`; few-shot; validation/retry; организация промпта; evaluation.
5. **D5 (15%, high)** — context window / lost-in-the-middle; durable facts / case-facts; summarization/compaction; prompt caching (уровень «когда использовать»); Batch API; crash recovery; telemetry/evaluation.

## P2 — сквозное суждение

6. **Production judgment** — accuracy / latency / cost; prompt injection и недоверенные tool results; least privilege; audit logs; privacy/compliance; выбор модели; streaming; non-determinism.

## P3 — hands-on (не зубрёжка SDK)

7. Один Messages API tool-loop · один structured extraction pipeline · один MCP tool · один Claude Code repo workflow. Текущий surface — из официальных docs, не из старых списков методов.

## Порядок первых сессий (рекомендация)

| # | Фокус | Task IDs |
|---|--------|----------|
| 1 | Agentic loop + `stop_reason` + coordinator/handoff (**done 2026-09-16 morning**) | 1.1–1.3, 1.6, 1.4🟡 |
| 2 | Coordinator / subagent + изоляция контекста | 1.2, 1.3 |
| 3 | Hooks vs prompt-only | 1.5, 2.x hooks |
| 4 | CLAUDE.md + rules globs | 3.1, 3.3 |
| 5 | Tool schemas + `tool_choice` | 2.1, 2.3, 4.3 |
| 6 | Case-facts / durable state | 5.1 |
