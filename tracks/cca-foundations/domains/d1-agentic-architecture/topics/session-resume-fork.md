# Сессии Agent SDK: resume и fork

**Slug:** `cca-f-d1-session-resume-fork`  
**Дата:** 2026-09-20 утро (Europe/Chisinau)  
**Домен:** D1 · task **1.7** ✅  
**Notebook:** https://notebook.google.com/notebook/6dbfad7c-6947-455b-84a5-5d509e3581b5

## Ядро

1. **continue** — latest сессия в cwd; ID не нужен.
2. **resume=session_id** — конкретная сессия (multi-user / не latest).
3. **fork** (`fork_session` / `forkSession` / `--fork-session` / `/branch`) — копия истории + новый ID; оригинал цел.
4. Fork ветвит **разговор**, не FS; правки файлов форком реальны → file checkpointing.
5. `session_id` с `ResultMessage` (и при error); TS ещё init `SystemMessage`.
6. После `error_max_turns` / budget — **resume** с большим лимитом, не cold start.
7. Cross-host: `SessionStore` / copy jsonl / case-facts в fresh session.

## Официальные источники

- https://code.claude.com/docs/en/agent-sdk/sessions
- https://code.claude.com/docs/en/sessions
- https://code.claude.com/docs/en/cli-reference

## Артефакты drop

- Work dir: `/workspace/cca-f-2026-09-20-morning/`
- Описательный PDF + presentation PDF + NotebookLM studio
- Practice: [`../../practice/2026-09-20-morning-session.md`](../../practice/2026-09-20-morning-session.md)
