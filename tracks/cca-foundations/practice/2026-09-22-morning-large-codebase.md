# Practice — 2026-09-22 утро · D5 5.4 large codebase context

5 exam-style Q (короткие ответы). Полный разбор — в opisatelnyj-doc drop.

## Q1 · Explore-first
OAuth в монорепо ~2M LOC.  
**Ответ:** Plan Mode / explorer (Glob/Grep/Read) → structured map → план → edits; не paste tree и не сразу Edit.

## Q2 · Subagent return
Что возвращает explorer координатору?  
**Ответ:** structured findings (entry points, symbols, invariants, risks, confidence); не полные файлы и не «наследование» контекста.

## Q3 · Compaction + facts
Сессия у лимита; нужно сохранить «не трогать generated/» и entry path.  
**Ответ:** durable case-facts / plan-файл + `/compact` с фокусом (или `/clear` + pinned); не bigger-window-only.

## Q4 · CLAUDE.md bloat
Корневой CLAUDE.md покрывает api+web+shared+mobile.  
**Ответ:** тонкий root + nested CLAUDE.md / `.claude/rules` paths + Skills on-demand.

## Q5 · LITM
30 полных файлов в середину длинного чата.  
**Ответ:** lost-in-the-middle + шум; targeting + summary вместо dump.
