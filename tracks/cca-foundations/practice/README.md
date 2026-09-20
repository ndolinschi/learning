# Practice notes (CCA-F)

Место для заметок по practice-вопросам и разборам сценариев.

**Покрыто: 40** (сессии 2026-09-16 morning — agent loops; 2026-09-17 morning — MCP/tools; 2026-09-17 evening — CLAUDE.md; 2026-09-18 morning — structured output; 2026-09-18 evening — context management; 2026-09-19 morning — Agent SDK hooks; 2026-09-19 evening — built-in tools + MCP deep; 2026-09-20 morning — session resume/fork).

## Оговорка

Публичные sample / «exam-pattern» материалы — **не аутентифицированные live-вопросы Anthropic**. Ниже — **темы** из research (независимые гайды и paraphrases), без претензии на совпадение с текущим экзаменом.

## Темы sample Q из research (для ориентации формы)

| Тема | Ключевой правильный рычаг (pattern) |
|------|-------------------------------------|
| Programmatic prerequisites vs prompts | Детерминированный gate/hook, не «усилить system prompt» |
| Качество tool description | Описания как primary selector; границы и примеры |
| Escalation calibration + few-shot | Явные критерии + примеры, не confidence/sentiment proxy |
| Project vs user slash commands | `.claude/commands/` в репо для команды |
| Plan mode для архитектуры | Plan Mode до больших multi-file / boundary decisions |
| Path-specific rules globs | `.claude/rules` + `paths:` для условных конвенций |
| Узкая task decomposition | Чинить coordinator decomposition, не temperature synthesis |
| Structured error propagation | Классифицированный error context координатору |
| Scoped tools для synthesis | Least privilege: узкий verify tool, не все search tools |
| Claude Code `-p` в CI | Non-interactive mode для пайплайнов |
| Message Batches только non-blocking | Не для blocking pre-merge checks |
| Forced tool_choice + input_schema extraction | Schema + forced tool + escapes + semantic retry |
| Few-shot label imbalance | 3–5 balanced examples; не majority-class dump |
| Schema vs semantic validation | Targeted repair, не blind retry |
| Native structured outputs | output_config.format / strict:true + semantic |
| Explicit criteria vs conservative | Measurable rules, не vague confidence |
| Multi-pass file review | Per-file + integration pass, не один монолитный контекст |
| `is_error` / retryable vs not | Structured tool_result, не silent empty success |
| `tool_choice` force extraction | any/tool (+ strict), не prose-only JSON |
| Project `.mcp.json` | Team-shared MCP; secrets via env expansion |
| CLAUDE.md hierarchy / concat | Project file в git; files concatenate, specific later |
| Skills vs monolithic CLAUDE.md | Процедуры → Skills (JIT) |
| Only personal CLAUDE.md | Дистрактор — нужен project file для команды |
| Persistent case-facts | Durable block вне summarised history |
| Lost-in-the-middle structure | Key Findings first + headers |
| Compaction preserve instructions | min 50k trigger; pass compaction block; lossy |
| Prompt caching layout | Stable prefix first + ephemeral; volatile last |
| Provenance multi-source | Conflicting claims + attribution; trim tools |
| PreToolUse refund/policy gate | Deterministic deny + reason, не prompt-only |
| Empty `{}` PreToolUse | No decision / normal permission flow |
| `updatedInput` sandbox rewrite | Normalize args under hookSpecificOutput |
| Hooks inside subagent | Same PreToolUse; agent_id/agent_type |
| Handoff after policy deny | HITL + case-facts (1.4), не blind retry |
| Built-in composition Glob→Read→Edit | Не Bash-sed для правок файлов |
| Review-subagent tool allowlist | Read/Grep/Glob/Bash без Write/Edit |
| MCP resource vs tool | `@` context vs side-effecting action |
| `mcp__server__tool` naming | Канон permissions/hooks/allowlist |
| Secrets via `${VAR}` / OAuth | Не CLAUDE.md; не plaintext в git |

Дополнительно (certified-architect.com samples, original for post): изоляция контекста subagent; persistent case-facts при summarization.

| Continue vs resume vs fork | continue=latest cwd; resume=ID; fork=copy+new ID |
| Fork ≠ filesystem sandbox | Conversation branch only; file edits real → checkpointing |
| Recovery after max_turns | Capture session_id from error ResultMessage → resume higher limit |
| Multi-tenant sessions | Per-entity session_id + resume, never shared continue |
| Cross-host session | SessionStore / copy jsonl / case-facts into fresh session |

## Как вести заметки

Для каждой разобранной practice-темы добавлять файл `YYYY-MM-DD-<slug>.md` и строку в таблицу ниже; обновлять счётчик «Покрыто».

| Дата | Тема | Домен | Заметки |
|------|------|-------|---------|
| 2026-09-16 | Agent loops, stop_reason, coordinator/subagents, handoff | D1 | [файл](2026-09-16-agent-loops.md) |
| 2026-09-17 | MCP, tool schemas, is_error, tool_choice, .mcp.json intro | D2 | [файл](2026-09-17-mcp-tools.md) |
| 2026-09-17 вечер | CLAUDE.md hierarchy, rules globs, Skills, Plan Mode | D3 | [файл](2026-09-17-evening-claude-md.md) |
| 2026-09-18 утро | Structured output: JSON Schema, tool_choice, few-shot, validation/retry | D4 | [файл](2026-09-18-morning.md) |
| 2026-09-18 вечер | Context management: case-facts, LITM, compaction, caching | D5 | [файл](2026-09-18-evening-context.md) |
| 2026-09-19 утро | Agent SDK hooks: Pre/PostToolUse, enforcement vs prompt-only | D1 | [файл](2026-09-19-morning-hooks.md) |
| 2026-09-19 вечер | Built-in Claude Code tools + MCP resources/prompts/auth | D2 | [файл](2026-09-19-evening-builtin-mcp.md) |
| 2026-09-20 утро | Session resume/fork: continue, resume, fork_session, CLI --fork-session /branch | D1 | [файл](2026-09-20-morning-session.md) |
