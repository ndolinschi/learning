# CCA-F: покрытие

**Общий прогресс: ~27%** (обновлено 2026-09-20 вечер, Europe/Chisinau)

> Оценка консервативная: девять тем + 45 practice Q. Exam readiness по домену ниже доли строк карты.

## Формула взвешенного прогресса

```
overall% = 0.27·D1% + 0.18·D2% + 0.20·D3% + 0.20·D4% + 0.15·D5%
```

Статусы строк карты: `✅` = 1, `🟡` = 0.5, `⬜` = 0 (для трекинга задач).  
**Exam-readiness % домена** (колонка ниже) — отдельная консервативная оценка готовности к scenario MCQ.

## По доменам

| Домен | Вес | Задач | ✅ | 🟡 | ⬜ | % строк карты | Exam-ready % | Вклад overall (exam-ready) |
|-------|----:|------:|--:|--:|--:|-------------:|-------------:|---------------------------:|
| [D1 Agentic Architecture](domains/d1-agentic-architecture/map.md) | 27% | 7 | 7 | 0 | 0 | **~100%** | **~36%** | ~9.7 |
| [D2 Tools & MCP](domains/d2-tools-mcp/map.md) | 18% | 5 | 5 | 0 | 0 | **~100%** | **~29%** | ~5.2 |
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 3 | 3 | 0 | **~75%** | **~28%** | ~5.6 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 3 | 1 | 2 | ~58% | **~20%** | ~4.0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 1 | 1 | 4 | ~25% | **~19%** | ~2.9 |
| **Итого** | **100%** | **30** | **19** | **5** | **6** | — | — | **~27%** |

Проверка: 0.27·36 + 0.18·29 + 0.20·28 + 0.20·20 + 0.15·19 ≈ 9.72+5.22+5.6+4.0+2.85 ≈ **27.39% ≈ 27%**.

## Что открыл drop 2026-09-20 evening (D3 CI/CD -p + iterative refinement)

- `claude -p` / `--print` headless; `--bare` для CI; stdin pipe ≤10MB
- `--output-format json|stream-json` + `--json-schema` → `structured_output`
- Permissions: `--allowedTools`, `--permission-mode`, `--permission-prompts none`, `--max-turns`
- GitHub Actions: automation `prompt` + `claude_args`; project CLAUDE.md в git
- Iterative refinement: Explore (Plan Mode) → Plan → small diffs → verify → adversarial subagent
- Дистракторы: edit-before-explore; monolithic PR; personal-only CLAUDE.md; unbounded CI loops
- 5 practice Q (итого ~45)
- **Карта D3:** 3.5+3.6 ✅ → 4.5/6 (~75%); exam-ready ~20%→~28%

## Что открыл drop 2026-09-20 morning (D1 session resume/fork)

- continue vs resume vs fork; session_id; D1 карта 7/7; exam-ready ~36%

## Что открыл drop 2026-09-19 evening (D2 built-in + MCP deep)

- Built-in roles + MCP resources/prompts/auth; карта D2 5/5

## Что открыл drop 2026-09-19 morning (D1 hooks)

- Pre/PostToolUse; enforcement vs prompt-only; handoff 1.4

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

- D1: карта закрыта; contradiction/verification drills + hands-on Messages API tool-loop
- D2: карта закрыта; hands-on MCP server + live Claude Code workflow
- D3: карта 4.5/6; углубить 3.2–3.4 (Skills deep / rules monorepo / Plan Mode scenarios); permissions layers hands-on
- D4: Batches (4.5), multi-pass (4.6); hands-on extraction pipeline
- D5: 5.2 escalation deep; 5.3–5.5; hands-on compaction loop
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **45** (девять сессий).
