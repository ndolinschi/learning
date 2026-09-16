# CCA-F: покрытие

**Общий прогресс: ~6%** (обновлено 2026-09-16 утро, Europe/Chisinau)

> Оценка консервативная: одна тема D1 + 5 practice Q. Не путать с «закрыто 4.5/7 task rows» — exam readiness по домену ниже доли строк карты.

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
| [D2 Tools & MCP](domains/d2-tools-mcp/map.md) | 18% | 5 | 0 | 0 | 5 | 0% | 0% | 0 |
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 0 | 0 | 6 | 0% | 0% | 0 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 0 | 0 | 6 | 0% | 0% | 0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 0 | 0 | 6 | 0% | ~2%* | ~0.3 |
| **Итого** | **100%** | **30** | **4** | **1** | **25** | — | — | **~6%** |

\*D5 ~2%: только кросс-касание durable/case-facts из темы D1; отдельной темы D5 не было.

## Что открыл drop 2026-09-16 morning

- Agent loop на `stop_reason` (не prose parsing)
- Workflow vs agent
- Hub-and-spoke coordinator / scoped subagents / explicit handoff
- Semantic decomposition
- Bounded retries, structured errors, HITL (вводно)
- Привязка к Multi-Agent Research + Customer Support
- 5 practice Q с ответами

## Остающиеся слабые зоны

- D1: Pre/PostToolUse hooks drill; session resume/fork; больше contradiction/verification drills
- D2–D4 полностью; D5 почти полностью
- Hands-on Messages API loop / MCP / Claude Code repo

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **5** (одна сессия).
