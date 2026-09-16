# Practice Q&A — D1 Agent loops / orchestration (2026-09-16)

**Метка:** practice / exam-pattern. **Не** официальные вопросы Anthropic. Парафраз публичных тем и сценариев.

## Q1 — Управление циклом
Агент поддержки вызывает tools до решения тикета. Правильный механизм?
- ❌ Парсить текст («calling tool…»)
- ✅ Ветвиться по `stop_reason == tool_use` и блокам `tool_use`
- ❌ Фиксировать ровно N вызовов API
- ❌ Поднять temperature

## Q2 — Декомпозиция research
Отчёт «культуры» покрыл только visual arts; music/writing нет. Что чинить?
- ✅ Семантическую декомпозицию координатора перед spawn
- ❌ Temperature синтезатора / промпт одного visual-исследователя / max_tokens отчёта

## Q3 — Изоляция контекста
Субагент refund eligibility не получил `order_id` и политику.
- ✅ Явно передать факты + scoped tool list в assignment
- ❌ Считать, что история координатора наследуется

## Q4 — Timeout
`search_docs` по таймауту вернул `[]`, цикл пошёл в синтез как success.
- ✅ Structured error (`isError`, class, retryable) + bounded retry/escalation
- ❌ Считать пустой список успехом

## Q5 — Agent vs workflow
Пайплайн extract → validate → DB всегда одинаковый.
- ✅ Детерминированный workflow (часто schema + `tool_choice`); агент — только при open-ended ветках
