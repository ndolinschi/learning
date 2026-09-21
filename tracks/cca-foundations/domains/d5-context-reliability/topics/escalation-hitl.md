# Эскалация, неоднозначность и HITL

- **Дата:** 2026-09-21 вечер (Europe/Chisinau)
- **Slug:** `cca-f-d5-escalation-hitl`
- **Tasks:** 5.2 ✅ · 5.3 ✅ · 5.5 ✅ · 5.6 ✅ (deepen) · 5.4 ⬜
- **Notebook:** https://notebook.google.com/notebook/6d64abc4-6a01-46f2-83c5-6ffaa924fc76

## Суть

Детерминированные критерии эскалации и structured error/HITL handoff важнее sentiment и самооценки модели. Multi-agent никогда не маскирует timeout пустым успехом; provenance сохраняет конфликты.

## Ключевые рычаги

1. Измеримые триггеры (cap, verify fail, explicit human, policy exception, retry exhausted)
2. Ambiguity: clarify / escalate / documented assumption
3. Structured escalate packet + case-facts snapshot
4. `is_error` + structured timeout/error к координатору
5. HITL tiers; confidence как route, не как hard gate
6. Preserve contradictions with attribution

## Practice

[`../../../practice/2026-09-21-evening-escalation-hitl.md`](../../../practice/2026-09-21-evening-escalation-hitl.md)