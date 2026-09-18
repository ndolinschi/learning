# CCA-F: приоритетный backlog

Приоритет = **вес домена × вероятность появления** (по research 2026-09-15: scenario density + независимые гайды).  
Не слепая ротация тем — только исследованные рычаги экзамена.

## P0 — максимальный рычаг

1. **D1 (27%, very high)** — agent vs workflow; циклы и `stop_reason`; coordinator/subagent; явная передача состояния; persistence; escalation; timeouts; bounded retries; human-in-the-loop. **Частично done 2026-09-16 morning.**
2. **D3 (20%, very high)** — иерархия CLAUDE.md; Plan Mode; Skills; `.claude/rules` globs; targeting контекста; permissions; scoped subagents; границы CI/CD review. **CLAUDE.md + rules + Skills + Plan Mode light — done 2026-09-17 evening; CI/CD `-p` (3.6) и iterative refinement (3.5) — ещё нет.**
3. **D2 (18%, very high по плотности сценариев)** — описания/схемы tools; MCP servers/resources/prompts; custom vs built-in vs MCP vs Skills; Pre/PostToolUse hooks; изоляция ошибок. **Ядро schemas/isError/tool_choice/.mcp.json intro — done 2026-09-17 morning; 2.5 built-in + deep MCP auth/resources — ещё нет.**

## P1 — высокий

4. **D4 (20%, high)** — system prompts; structured output через tools; schema vs prose; forced `tool_choice`; few-shot; validation/retry; организация промпта; evaluation. **Ядро 4.2–4.4 + 4.1 light — done 2026-09-18 morning (~58% карты, exam-ready ~20%); Batches (4.5) и multi-pass (4.6) — ещё нет. Также: newer `output_config`/`strict` awareness.**
5. **D5 (15%, high)** — context window / lost-in-the-middle; durable facts / case-facts; summarization/compaction; prompt caching (уровень «когда использовать»); Batch API; crash recovery; telemetry/evaluation. **Case-facts + LITM + compaction + caching + provenance light — done 2026-09-18 evening (5.1 ✅, 5.6 🟡; exam-ready ~19%, карта ~25%). Deep 5.2–5.5 — ещё нет.**

## P2 — сквозное суждение

6. **Production judgment** — accuracy / latency / cost; prompt injection и недоверенные tool results; least privilege; audit logs; privacy/compliance; выбор модели; streaming; non-determinism.

## P3 — hands-on (не зубрёжка SDK)

7. Один Messages API tool-loop · один structured extraction pipeline · один MCP tool · один Claude Code repo workflow. Текущий surface — из официальных docs, не из старых списков методов.

## Порядок первых сессий (рекомендация)

| # | Фокус | Task IDs |
|---|--------|----------|
| 1 | Agentic loop + `stop_reason` + coordinator/handoff (**done 2026-09-16 morning**) | 1.1–1.3, 1.6, 1.4🟡 |
| 2 | Tool schemas + `tool_choice` + isError + `.mcp.json` intro (**done 2026-09-17 morning**) | 2.1–2.3 ✅, 2.4🟡 |
| 3 | CLAUDE.md + rules globs + Skills + Plan Mode light (**done 2026-09-17 evening**) | 3.1 ✅, 3.2🟡, 3.3🟡, 3.4🟡 |
| 4 | Structured output: schema + forced tool_choice + few-shot + validation (**done 2026-09-18 morning**) | 4.1🟡, 4.2–4.4 ✅ |
| 5 | Case-facts / durable state + compaction + caching (**done 2026-09-18 evening**) | 5.1 ✅, 5.6🟡 |
| 6 | Hooks vs prompt-only | 1.5, 2.x hooks |
| 7 | Built-in Claude Code tools / MCP deep | 2.5, 2.4 remainder |
| 8 | Escalation deep · 5.3–5.5 · CI/CD `-p` · Batches · multi-pass | 5.2–5.5, 3.6, 4.5, 4.6 |
