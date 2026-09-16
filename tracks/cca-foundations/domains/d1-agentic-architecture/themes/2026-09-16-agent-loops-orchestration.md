# Агентные циклы и оркестрация: stop_reason, координатор и субагенты, явная передача состояния

- **Трек:** CCA-F Foundations
- **Домен:** D1 Agentic Architecture & Orchestration (~27%)
- **Дата:** 2026-09-16 (утро, Europe/Chisinau)
- **Статус:** ✅ (закрывает task 1.1, 1.2, 1.3, 1.6 частично 1.4; 1.5 hooks и 1.7 session fork — ещё нет)
- **Notebook:** https://notebook.google.com/notebook/30e9f8c4-e787-4a34-92a7-8783f5ab91dc
- **Practice:** [`../../../practice/2026-09-16-agent-loops.md`](../../../practice/2026-09-16-agent-loops.md)

## Кратко

Управление агентом — цикл по `stop_reason` Messages API. Сложные задачи — hub-and-spoke: семантическая декомпозиция, субагенты с явным заданием и scoped tools (без наследования истории), durable/case-facts, bounded retries и HITL. Workflow предпочтительнее агента, когда путь шагов известен.

## Ключевые идеи

1. Ветвление по `stop_reason` (`tool_use`, `end_turn`, `max_tokens`, `pause_turn`, …), не парсинг prose.
2. Agent vs deterministic workflow — по предсказуемости пути.
3. Coordinator-mediated routing; peer-to-peer субагентов — антипаттерн по умолчанию.
4. Явный handoff состояния; субагент стартует «пустым».
5. Structured errors вместо silent empty success; эскалация человеку.

## Task IDs

| ID | Эффект этого дропа |
|----|--------------------|
| 1.1 agentic loops / stop_reason | ✅ |
| 1.2 coordinator-subagent hub-and-spoke | ✅ |
| 1.3 context passing / spawning | ✅ |
| 1.4 multi-step workflows / handoff | 🟡 (handoff + workflow vs agent; enforcement hooks — нет) |
| 1.5 Agent SDK hooks Pre/PostToolUse | ⬜ (упомянуты refund cap как принцип) |
| 1.6 task decomposition | ✅ |
| 1.7 session resume/fork | ⬜ |

## Материалы

- Полный research / doc PDF: `/workspace/cca-f-2026-09-16-morning/` на Learning Bot box
- Источники: platform.claude.com (stop reasons, tool use); Anthropic Engineering (Building Effective Agents; Multi-Agent Research); Managed Agents multiagent; exam-reality-brief
