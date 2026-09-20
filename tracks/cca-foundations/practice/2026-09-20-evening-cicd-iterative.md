# Practice: CI/CD `-p` + iterative refinement (2026-09-20 вечер)

Домен D3 · tasks 3.5 + 3.6 · 5 scenario Q

## Q1 — CI typo-bot
**Правильный рычаг:** `git diff | claude -p` (или Action с `prompt` + allowlist).  
**Дистракторы:** interactive без TTY; только personal CLAUDE.md; `while true` без `--max-turns`.

## Q2 — Structured flaky gate
**Правильный рычаг:** `-p` + `--output-format json` + `--json-schema` → `structured_output.is_flaky`.  
**Дистракторы:** text+regex; `--output-format json` без `-p`; ручная interactive сессия.

## Q3 — Multi-file OAuth
**Правильный рычаг:** Plan Mode explore → plan → small diffs + tests → subagent review.  
**Дистракторы:** edit-before-explore; один unbounded `-p` на весь репо; выключить verification.

## Q4 — Night unattended job
**Правильный рычаг:** `-p` + `acceptEdits`/`auto` + `--permission-prompts none` + `--max-turns` + Secrets.  
**Дистракторы:** Manual+человек; API key в YAML; `-p`+`--bg`.

## Q5 — PR на 80 файлов
**Анти-паттерн (ответ «хуже»):** monolithic весь diff в одном контексте.  
**Паттерн:** scoped/multi-pass + review subagent Read-only + schema summary.

## Ключевые факты
- `--bare` для детерминированного CI; `ANTHROPIC_API_KEY`
- Project CLAUDE.md в git (не personal-only)
- Iterative = Explore→Plan→Implement→Verify→Refine; two-pass adversarial
