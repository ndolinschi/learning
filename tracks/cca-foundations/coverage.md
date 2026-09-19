# CCA-F: покрытие

**Общий прогресс: ~23%** (обновлено 2026-09-19 утро, Europe/Chisinau)

> Оценка консервативная: шесть тем (D1–D5 + D1 hooks) + 30 practice Q. Exam readiness по домену ниже доли строк карты.

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
| [D2 Tools & MCP](domains/d2-tools-mcp/map.md) | 18% | 5 | 3 | 1 | 1 | ~70% | **~22%** | ~4.0 |
| [D3 Claude Code](domains/d3-claude-code/map.md) | 20% | 6 | 1 | 3 | 2 | ~42% | **~20%** | ~4.0 |
| [D4 Prompt & Structured](domains/d4-prompt-structured/map.md) | 20% | 6 | 3 | 1 | 2 | ~58% | **~20%** | ~4.0 |
| [D5 Context & Reliability](domains/d5-context-reliability/map.md) | 15% | 6 | 1 | 1 | 4 | ~25% | **~19%** | ~2.9 |
| **Итого** | **100%** | **30** | **14** | **6** | **10** | — | — | **~23%** |

Проверка: 0.27·30 + 0.18·22 + 0.20·20 + 0.20·20 + 0.15·19 ≈ 8.1+3.96+4.0+4.0+2.85 ≈ **22.91% ≈ 23%**.

## Что открыл drop 2026-09-19 morning (D1 hooks)

- PreToolUse / PostToolUse / PostToolUseFailure — когда fire в agentic loop
- `permissionDecision`: allow | deny | ask | defer; приоритет deny > defer > ask > allow
- `updatedInput` (нормализация args) + `permissionDecisionReason` + `systemMessage`
- PostToolUse: `additionalContext`, `updatedToolOutput` (не «отмена» tool)
- Пустой `{}` = no decision (не allow); silent `[]` / empty success — ловушка
- Enforcement vs prompt-only: refund-cap / policy gates через hooks
- Hooks внутри subagent (`agent_id`/`agent_type`); handoff 1.4 после deny
- Related: MCP matchers `mcp__…` (D2); SessionStart resume/fork (1.7) — только mention
- 5 practice Q
- **Не покрыто deep:** 1.7 session resume/fork drill

## Что открыл drop 2026-09-18 evening (D5)

- Case-facts / durable facts block вне summarised history (CORE 5.1)
- Lost-in-the-middle: Key Findings Summary first + headers
- Compaction API: `compact_20260112`, trigger min 50k, custom `instructions`, pass compaction block back, `pause_after_compaction`
- Trim / clear re-fetchable tool results (JIT)
- Prompt caching: stable prefix first + `cache_control` ephemeral (~5 мин)
- Provenance light (5.6): conflicting claims + attribution
- Escalation criteria (5.2) — только related mention
- 5 practice Q
- **Не покрыто deep:** 5.3 error propagation, 5.4 large-codebase, 5.5 HITL calibration

## Что открыл drop 2026-09-18 morning (D4)

- Exam-core: tool `input_schema` + forced `tool_choice` + nullable/enum escapes
- Newer official: `output_config.format` (JSON outputs) + `strict: true` (strict tool use) — [VSCOPproduction
- Few-shot 3–5 balanced; trap = label imbalance
- Explicit criteria vs «be conservative»
- Schema vs semantic validation + targeted retry; programmatic > prompt-only
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

- D1: session resume/fork (1.7); contradiction/verification drills
- D2: built-in Claude Code tools (2.5); MCP auth/resources deep; hooks overlap practice
- D3: CI/CD `-p` (3.6); iterative refinement (3.5); permissions layers
- D4: Batches (4.5), multi-pass (4.6); hands-on extraction pipeline
- D5: 5.2 escalation deep; 5.3–5.5; hands-on compaction loop
- Hands-on Messages API tool-loop / Claude Code repo workflow

Practice notes: [`practice/README.md`](practice/README.md) — покрыто **30** (шесть сессий).
