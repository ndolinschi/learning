# Practice — 2026-09-18 evening · D5 Context Management

> Practice / exam-pattern — **не** официальные вопросы Anthropic.

## Q1 Case-facts

После summarisation агент забывает $247.83 / #8891. Лучший фикс?

**Ответ:** persistent case-facts block в каждый prompt, вне summarised history (не bigger window; не «preserve numbers in summary» alone).

## Q2 Lost-in-the-middle

Synthesis теряет цифры из середины длинных субагент-отчётов.

**Ответ:** Key Findings Summary в начало + секции с заголовками (структурный фикс, не «read carefully»).

## Q3 Compaction

Верно про server-side compaction?

**Ответ:** trigger `input_tokens` min **50k**; custom `instructions` заменяют default; блок `compaction` вернуть; lossy — case-facts снаружи.

## Q4 Prompt caching

Какой layout даёт hit?

**Ответ:** system/tools/docs first + `cache_control` ephemeral; volatile user turn last. Dynamic-first = miss.

## Q5 Provenance + tools

Два источника с разными цифрами; tool вернул 40 полей.

**Ответ:** оба claim с attribution в Key Findings; trim tool fields; спорное пометить — не усреднять без источников.
