# CCA-F: покрытие

**Общий прогресс: ~10%** (обновлено 2026-09-17 утро, Europe/Chisinau)

> Оценка консервативная: две темы (D1 + D2) + 10 practice Q. Exam readiness по домену ниже доли строк карты.

## Формула взвешенного прогресса

```
overall% = 0.27·D1% + 0.18·D2% + 0.20·D3% + 0.20·D4% + 0.15·D5%
```

Статусы строк карты: `✅` = 1, `🟡` = 0.5, `⬜` = 0 (для трекинга задач).  
**Exam-readiness % домена** (колонка ниже) — отдельная консервативная оценка готовности к scenario MCQ.

## По доменам

| Домен | Вес | Задач | ✅ | 🟡 | ⬜ | % строк карты | Exam-ready % | Вклад overall (exam-ready) |
|-------|----:|------:|--:|--:|--:|-------------:|-------------:|---------------------------:|
| [D1 Agentic Architecture](domains/d1-agentic-architecture/map.md) | 27% | 7 | 4 | 1 | 2 | ~64% | **~22%** | ~5.9 |
| [D2 Tools & MCP](domains/d2-tools-mcp/map.md) | 18% | 5 | 3 | 1 | 1 | ~70% | **~22%** | ~4.0 |
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 0 | 0 | 6 | 0% | 0% | 0 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 0 | 0 | 6 | 0% | 0% | 0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 0 | 0 | 6 | 0% | ~2%* | ~0.3 |
| **Итого** | **100%** | **30** | **7** | **2** | **21** | — | — | **~10%** |

\*D5 ~2%: только кросс-касание durable/case-facts из темы D1; отдельной темы D5 не было.

## Что открыл drop 2026-09-17 morning (D2)

- Дизайн tool interfaces: descriptions как primary selector, schema, границы read/write
- Структурированные ошибки tools / MCP: `is_error`, retryable vs not
- `tool_choice` (`auto`/`any`/`tool`/`none`) + `disable_parallel_tool_use`
- Распределение tools по ролям + MCPToolset allowlist/denylist
- Intro Claude Code `.mcp.json` (project/user/local) и MCP connector Messages API
- 5 practice Q (Customer Support registry, Dev Productivity, synthesizer scope)

## Что открыл drop 2026-09-16 morning (D1)

- Agent loop на `stop_reason` (не prose parsing)
- Workflow vs agent; hub-and-spoke; semantic decomposition; bounded retries / HITL

## Остающиеся слабые зоны

- D1: Pre/PostToolUse hooks drill; session resume/fork; contradiction/verification drills
- D2: built-in Claude Code tools (2.5); hands-on MCP server; OAuth/SSE deep dive; `.mcp.json` resources/prompts beyond intro
- D3–D4 полностью; D5 почти полностью
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **10** (две сессии).
