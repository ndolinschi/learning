# CCA-F: покрытие

**Общий прогресс: ~24%** (обновлено 2026-09-19 вечер, Europe/Chisinau)

> Оценка консервативная: семь тем (D1–D5 + D1 hooks + D2 built-in/MCP deep) + 35 practice Q. Exam readiness по домену ниже доли строк карты.

## Формула взвешенного прогресса

```
overall% = 0.27·D1% + 0.18·D2% + 0.20·D3% + 0.20·D4% + 0.15·D5%
```

Статусы строк карты: `✅` = 1, `🟡` = 0.5, `⬜` = 0 (для трекинга задач).  
**Exam-readiness % домена** (колонка ниже) — отдельная консервативная оценка готовности к scenario MCQ.

## По доменам

| Домен | Вес | Задач | ✅ | 🟡 | ⬜ | % строк карты | Exam-ready % | Вклад overall (exam-ready) |
|-------|----:|------:|--:|--:|--:|-------------:|-------------:|---------------------------:|
| [D1 Agentic Architecture](domains/d1-agentic-architecture/map.md) | 27% | 7 | 6 | 0 | 1 | ~86% | **~30%** | ~8.1 |
| [D2 Tools & MCP](domains/d2-tools-mcp/map.md) | 18% | 5 | 5 | 0 | 0 | **~100%** | **~29%** | ~5.2 |
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 1 | 3 | 2 | ~42% | **~20%** | ~4.0 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 3 | 1 | 2 | ~58% | **~20%** | ~4.0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 1 | 1 | 4 | ~25% | **~19%** | ~2.9 |
| **Итого** | **100%** | **30** | **16** | **5** | **9** | — | — | **~24%** |

Проверка: 0.27·30 + 0.18·29 + 0.20·20 + 0.20·20 + 0.15·19 ≈ 8.1+5.22+4.0+4.0+2.85 ≈ **24.17% ≈ 24%**.

## Что открыл drop 2026-09-19 evening (D2 built-in + MCP deep)

- Built-in roles: Read / Write / Edit / Bash / Grep / Glob + композиция поиска→чтения→правки
- Edit exact match / uniqueness / read-before-edit; Write vs Edit
- Bash не как замена Edit; platform note Glob/Grep absent-by-default на Unix
- Permission path: `Edit(path)` покрывает Write; path-`Write(…)` не консультируется
- Scoped subagent tool lists; `--tools` vs MCP deny
- Trade-offs: built-in vs custom vs MCP vs Skills
- MCP resources (`@`) vs tools vs prompts (slash); scopes local/project/user
- Env `${VAR}` / OAuth / oauth.scopes / headersHelper; `mcp__server__tool`
- 5 practice Q; reinforce instructive errors / is_error
- **Карта D2:** 5/5 ✅ (~100%); exam-ready ~22%→~29%

## Что открыл drop 2026-09-19 morning (D1 hooks)

- PreToolUse / PostToolUse / PostToolUseFailure; permissionDecision; updatedInput
- Enforcement vs prompt-only; hooks в subagent; handoff 1.4
- 5 practice Q; 1.7 session — только mention

## Что открыл drop 2026-09-18 evening (D5)

- Case-facts, LITM, compaction, caching, provenance light

## Что открыл drop 2026-09-18 morning (D4)

- Structured output: schema + tool_choice + few-shot + validation

## Что открыл drop 2026-09-17 evening (D3)

- CLAUDE.md hierarchy, rules, Skills, Plan Mode light

## Что открыл drop 2026-09-17 morning (D2)

- Tool interfaces, is_error, tool_choice, `.mcp.json` intro

## Что открыл drop 2026-09-16 morning (D1)

- Agent loop на stop_reason; hub-and-spoke; bounded retries / HITL

## Остающиеся слабые зоны

- D1: session resume/fork (1.7); contradiction/verification drills
- D2: карта закрыта; углубление hands-on MCP server + live Claude Code workflow
- D3: CI/CD `-p` (3.6); iterative refinement (3.5); permissions layers
- D4: Batches (4.5), multi-pass (4.6); hands-on extraction pipeline
- D5: 5.2 escalation deep; 5.3–5.5; hands-on compaction loop
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **35** (семь сессий).
