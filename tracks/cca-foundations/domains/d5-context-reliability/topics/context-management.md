# Управление контекстом: case-facts, LITM, compaction, caching

**Slug:** `cca-f-d5-context-management`  
**Дата:** 2026-09-18 вечер (Europe/Chisinau)  
**Домен:** D5 Context Management & Reliability (15%)  
**Tasks:** 5.1 ✅ · 5.6 🟡 · 5.2 related mention  

## Ключевые takeaways

1. **Case-facts block** — ID/суммы/даты/constraints каждый ход **вне** summarised history. Не «bigger window».
2. **Lost-in-the-middle** — Key Findings Summary в начало + заголовки секций.
3. **Compaction** — `context_management` / `compact_20260112`; trigger `input_tokens` min **50k**; custom `instructions` PRESERVE; lossy; вернуть compaction block; опц. `pause_after_compaction`.
4. **Trim/clear tools** — урезать verbose results; clear re-fetchable (JIT).
5. **Prompt caching** — stable prefix first + `cache_control` ephemeral (~5 мин); volatile last.
6. **Provenance light** — conflicting claims с attribution.

## Дистракторы

- Увеличить context window  
- Суммировать всё включая case-facts  
- «Pay attention to the middle»  
- Dynamic content before static for caching  
- Rely on model memory (API stateless)

## Артефакты

- Workdir: `/workspace/cca-f-2026-09-18-evening/`
- Описательный PDF: `context-management-opisatelnyj-doc.pdf`
- Notebook source: `notebook-source.pdf`
- Presentation: `presentation-context-management.pdf` (после Studio)
- Practice: [`../../../practice/2026-09-18-evening-context.md`](../../../practice/2026-09-18-evening-context.md)

## Источники

- platform.claude.com docs: compaction, prompt caching  
- claudecertificationguide.com 5.1 Context Window Management  
