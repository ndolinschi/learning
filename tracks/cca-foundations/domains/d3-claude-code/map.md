# D3 — Claude Code Configuration & Workflows (20%)

Статусы: `✅` изучено · `🟡` частично · `⬜` не начато

| Тема/Task | Статус | Дата | Notebook | Заметки |
|-----------|--------|------|----------|---------|
| 3.1 Configure CLAUDE.md with hierarchy, scoping, and modular organization | ✅ | 2026-09-17 вечер | [Gemini Notebook](https://notebook.google.com/notebook/24dc1308-686f-4adc-af94-ce7eae420364) | managed/user/project/local; concat; @imports; &lt;200 строк |
| 3.2 Create and configure custom slash commands and skills | 🟡 | 2026-09-17 вечер | same | commands vs Skills; args; disable-model-invocation; без глубокого plugin eval |
| 3.3 Apply path-specific rules for conditional convention loading (`.claude/rules` globs) | 🟡 | 2026-09-17 вечер | same | `paths:` globs; user vs project rules; без monorepo excludes drill |
| 3.4 Determine when to use Plan Mode vs direct execution | 🟡 | 2026-09-17 вечер | same | light: Shift+Tab / `/plan`; explore before edit |
| 3.5 Apply iterative refinement techniques for progressive improvement | ⬜ | | | |
| 3.6 Integrate Claude Code into CI/CD pipelines (`-p`, structured output) | ⬜ | | | отложено намеренно |

**Покрытие домена: ~2.5/6 строк (~42% карты) · exam-ready ~20%**

## Ключевые сценарии / темы

- Code Generation with Claude Code: project `.claude/CLAUDE.md`, precedence/concat, Skills, `@`-imports, path-scoped rules  
- Claude Code in CI/CD: non-interactive `-p`, scoped review subagent, two-pass review (**ещё не drill**)  
- Distractors: «править до explore», monolithic context на больших PR, только personal CLAUDE.md  
- Explore / Plan Mode / scoped subagent с ограниченным tool list

## Тема вечера

[`topics/claude-md-hierarchy.md`](topics/claude-md-hierarchy.md)
