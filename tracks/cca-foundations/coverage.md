# CCA-F: покрытие

**Общий прогресс: ~32%** (обновлено 2026-09-22 утро, Europe/Chisinau)

> Оценка консервативная: двенадцать тем + 60 practice Q. Exam readiness по домену ниже доли строк карты.

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
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 5 | 1 | 0 | **~92%** | **~28%** | ~5.6 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 6 | 0 | 0 | **~100%** | **~36%** | ~5.4 |
| **Итого** | **100%** | **30** | **26** | **4** | **0** | — | — | **~32%** |

Проверка: 0.27·36 + 0.18·29 + 0.20·28 + 0.20·28 + 0.15·36 ≈ 9.72+5.22+5.6+5.6+5.4 ≈ **31.54% ≈ 32%**.

## Что открыл drop 2026-09-22 morning (D5 large codebase · 5.4)

- Explore-first / Plan Mode до multi-file правок
- Grep/Glob/Read/@ / LSP targeting вместо paste tree
- Scoped explorer-субагент: Read/Grep/Glob(+Bash) → structured findings
- Compaction exploration + pin durable case-facts (paths, invariants, APIs) — связь с 5.1
- Lean layered CLAUDE.md + `.claude/rules` paths + Skills on-demand; `claudeMdExcludes` / Read deny
- LITM при dump многих файлов в середину сессии
- 5 practice Q (итого ~60)
- **Карта D5:** 5.4 ✅ → **6/6 (~100%)**; exam-ready ~30%→~36%; overall ~31%→~32%

## Что открыл drop 2026-09-21 evening (D5 эскалация / HITL)

- Эскалация: явные измеримые критерии + few-shot; sentiment ортогонален политике
- Ambiguity triad: clarify / escalate / documented assumption
- Structured HITL packet; multi-agent `is_error`; калибровка; provenance 5.6
- 5 practice Q (итого ~55); карта D5 5/6 (~83%); exam-ready ~30%; overall ~31%

## Что открыл drop 2026-09-21 morning (D4 Message Batches + multi-pass)

- Message Batches + multi-pass; карта D4 5.5/6 (~92%); exam-ready ~28%; overall ~29%

## Что открыл drop 2026-09-20 evening (D3 CI/CD -p + iterative refinement)

- `claude -p` / structured output / iterative refinement; карта D3 4.5/6; exam-ready ~28%

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
- D4: карта 5.5/6 (4.1 🟡 explicit criteria); hands-on extraction+batch pipeline; `output_config`/`strict` drills
- D5: карта 6/6; hands-on compaction loop / live HITL webhook / live large-repo Claude Code drill
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **60** (двенадцать сессий).
