# Управление контекстом при исследовании больших кодовых баз

- **Дата:** 2026-09-22 утро (Europe/Chisinau)
- **Slug:** `cca-f-d5-large-codebase`
- **Task:** 5.4 ✅
- **Notebook:** https://notebook.google.com/notebook/dd222962-631b-4d39-931b-8d9d970b91f0

## Суть

Не топить контекстное окно деревом и сырыми файлами. Explore-first / Plan Mode; Grep/Glob/Read + LSP; scoped explorer-субагент со structured findings; lean layered CLAUDE.md / rules / Skills; pin durable case-facts после exploration (связь с 5.1).

## Ключевые рычаги

1. Explore → Plan → Implement (не edit-before-explore на multi-file)
2. Targeting вместо paste codebase
3. Subagent tools Read/Grep/Glob(+Bash) → structured findings, не dump
4. Compaction + case-facts (paths, invariants, decided APIs)
5. Layered CLAUDE.md / path-scoped rules / on-demand Skills
6. LITM: не запихивать десятки полных файлов в середину сессии

## Practice

[`../../../practice/2026-09-22-morning-large-codebase.md`](../../../practice/2026-09-22-morning-large-codebase.md)
