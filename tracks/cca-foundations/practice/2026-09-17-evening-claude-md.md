# Practice — 2026-09-17 вечер · D3 CLAUDE.md

*Exam-pattern / practice. Не официальные вопросы Anthropic.*

## Q1. Team standards
Команда хочет единые build/test и запрет push в main. Куда?
- ✅ Project `./CLAUDE.md` или `./.claude/CLAUDE.md` в git
- ❌ Только `~/.claude/CLAUDE.md`

## Q2. Path-scoped rules
API-правила засоряют контекст на фронте.
- ✅ `.claude/rules/api.md` с `paths: ["src/api/**"]`

## Q3. Skills vs CLAUDE.md
Deploy-чеклист раздул CLAUDE.md до 400 строк.
- ✅ Skill `.claude/skills/deploy/SKILL.md` (+ `disable-model-invocation: true` если только вручную)

## Q4. Concat / precedence
User «tabs» + project «2-space».
- ✅ Оба в контексте (concat); устранить конфликт; specific later

## Q5. Plan Mode
Рефакторинг незнакомого `src/billing/`.
- ✅ Plan Mode сначала; не сразу Edit / acceptEdits
