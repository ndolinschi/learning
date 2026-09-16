# D1 — Agentic Architecture & Orchestration (27%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 1.1 Design and implement agentic loops for autonomous task execution (`stop_reason`, tool_result append) | ✅ | 2026-09-16 | [notebook](https://notebook.google.com/notebook/30e9f8c4-e787-4a34-92a7-8783f5ab91dc) | [тема](themes/2026-09-16-agent-loops-orchestration.md) |
| 1.2 Orchestrate multi-agent systems with coordinator-subagent patterns (hub-and-spoke) | ✅ | 2026-09-16 | | [тема](themes/2026-09-16-agent-loops-orchestration.md) |
| 1.3 Configure subagent invocation, context passing, and spawning (isolated context) | ✅ | 2026-09-16 | | [тема](themes/2026-09-16-agent-loops-orchestration.md) |
| 1.4 Implement multi-step workflows with enforcement and handoff patterns | 🟡 | 2026-09-16 | | handoff + workflow vs agent; hooks enforcement → 1.5 |
| 1.5 Apply Agent SDK hooks for tool call interception and data normalization (Pre/PostToolUse) | ⬜ | | | refund-cap принцип упомянут; отдельный drill нужен |
| 1.6 Design task decomposition strategies for complex workflows | ✅ | 2026-09-16 | | [тема](themes/2026-09-16-agent-loops-orchestration.md) |
| 1.7 Manage session state, resumption, and forking (`--resume`, `fork_session`) | ⬜ | | | |

**Покрытие домена: 4.5/7 (~64%)** — консервативно для exam readiness по D1 считаем **~22%** (одна тема, без deep hooks/session SDK и без большого банка drills). См. `coverage.md`.

## Ключевые сценарии / темы

- Customer Support Resolution Agent: `stop_reason` loop, escalation handoff, refund/policy gates  
- Multi-Agent Research System: semantic decomposition, parallel researchers, verification с attribution  
- Явная передача фактов subagent (пустой контекст по умолчанию)  
- Детерминированные hooks vs prompt-only policy  
- Bounded retries / timeouts / structured error returns (не `[]` как success)
