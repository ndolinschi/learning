# CCA-F: покрытие

**Общий прогресс: ~18%** (обновлено 2026-09-18 утро, Europe/Chisinau)

> Оценка консервативная: четыре темы (D1 + D2 + D3 intro + D4 structured) + 20 practice Q. Exam readiness по домену ниже доли строк карты.

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
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 1 | 3 | 2 | ~42% | **~20%** | ~4.0 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 3 | 1 | 2 | ~58% | **~20%** | ~4.0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 0 | 0 | 6 | 0% | ~2%* | ~0.3 |
| **Итого** | **100%** | **30** | **11** | **6** | **13** | — | — | **~18%** |

\*D5 ~2%: только кросс-касание durable/case-facts из темы D1; отдельной темы D5 не было.

Проверка: 0.27·22 + 0.18·22 + 0.20·20 + 0.20·20 + 0.15·2 ≈ 5.94+3.96+4.0+4.0+0.3 ≈ **18.2%**.

## Что открыл drop 2026-09-18 morning (D4)

- Exam-core: tool `input_schema` + forced `tool_choice` + nullable/enum escapes
- Newer official: `output_config.format` (JSON outputs) + `strict: true` (strict tool use) — [VS]/production
- Few-shot 3–5 balanced; trap = label imbalance
- Explicit criteria vs «be conservative»
- Schema vs semantic validation + targeted retry; programmatic &gt; prompt-only
- SkillCert CLI `--json-schema` «100%» — overstated, не центр
- 5 practice Q (Structured Data Extraction style)
- **Не покрыто:** Message Batches (4.5), multi-pass/independent review (4.6)

## Что открыл drop 2026-09-17 evening (D3)

- Иерархия CLAUDE.md: managed / user / project / local; **concat**; lazy nested
- `@imports`, `.claude/rules` + `paths`, Skills vs slash, Plan Mode (light)
- 5 practice Q; CI/CD `-p` (3.6) отложен

## Что открыл drop 2026-09-17 morning (D2)

- Tool interfaces, `is_error`, `tool_choice`, `.mcp.json` intro

## Что открыл drop 2026-09-16 morning (D1)

- Agent loop на `stop_reason`; hub-and-spoke; bounded retries / HITL

## Остающиеся слабые зоны

- D1: Pre/PostToolUse hooks drill; session resume/fork; contradiction/verification
- D2: built-in Claude Code tools (2.5); MCP auth/resources deep
- D3: CI/CD `-p` (3.6); iterative refinement (3.5); permissions layers
- D4: Batches (4.5), multi-pass (4.6); hands-on extraction pipeline
- D5 почти полностью
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **20** (четыре сессии).
