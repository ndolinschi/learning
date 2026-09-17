# Practice notes (CCA-F)

Место для заметок по practice-вопросам и разборам сценариев.

**Покрыто: 15** (сессии 2026-09-16 morning — agent loops; 2026-09-17 morning — MCP/tools; 2026-09-17 evening — CLAUDE.md).

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
| Multi-pass file review | Per-file + integration pass, не один монолитный контекст |
| `is_error` / retryable vs not | Structured tool_result, не silent empty success |
| `tool_choice` force extraction | any/tool (+ strict), не prose-only JSON |
| Project `.mcp.json` | Team-shared MCP; secrets via env expansion |
| CLAUDE.md hierarchy / concat | Project file в git; files concatenate, specific later |
| Skills vs monolithic CLAUDE.md | Процедуры → Skills (JIT) |
| Only personal CLAUDE.md | Дистрактор — нужен project file для команды |

Дополнительно (certified-architect.com samples, original for post): изоляция контекста subagent; persistent case-facts при summarization.

## Как вести заметки

Для каждой разобранной practice-темы добавлять файл `YYYY-MM-DD-<slug>.md` и строку в таблицу ниже; обновлять счётчик «Покрыто».

| Дата | Тема | Домен | Заметки |
|------|------|-------|---------|
| 2026-09-16 | Agent loops, stop_reason, coordinator/subagents, handoff | D1 | [файл](2026-09-16-agent-loops.md) |
| 2026-09-17 | MCP, tool schemas, is_error, tool_choice, .mcp.json intro | D2 | [файл](2026-09-17-mcp-tools.md) |
| 2026-09-17 вечер | CLAUDE.md hierarchy, rules globs, Skills, Plan Mode | D3 | [файл](2026-09-17-evening-claude-md.md) |
