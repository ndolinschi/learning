# Practice — 2026-09-21 вечер · D5 эскалация / HITL

5 exam-style Q (короткие ответы). Полный разбор — в opisatelnyj-doc drop.

## Q1 · Escalation triggers
Sentiment-only escalate при валидном policy denial → ложные эскалации.  
**Ответ:** sentiment ортогонален; эскалировать по policy/ambiguity/explicit human; sentiment = tie-breaker.

## Q2 · Ambiguity
«Верните деньги» при 3 заказах без id → агент угадал largest.  
**Ответ:** clarify или escalate с partial_state; не silent guess на write-path.

## Q3 · Error propagation
Music subagent timeout → `[]` success → отчёт без музыки.  
**Ответ:** structured `{status:'timeout', is_error:true, ...}`; coordinator retry/mark gap.

## Q4 · HITL packet
Hook deny refund $620 > cap $500.  
**Ответ:** structured block (ids, amount, cap, blocked_tool, reason, recommended_action, case_facts) — не dump транскрипта.

## Q5 · Confidence + provenance
confidence 0.95 vs checksum fail; Pew 45% vs McKinsey 12%.  
**Ответ:** validation/hooks > self-confidence; сохранить оба % с attribution; сомнительные поля → human review.