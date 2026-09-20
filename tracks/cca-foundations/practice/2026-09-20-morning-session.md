# Practice — 2026-09-20 утро · D1 1.7 session resume/fork

5 exam-style Q (краткие ответы). Полные формулировки — в описательном PDF drop.

1. **max_turns + сохранённый ID** → `resume=session_id` с большим `max_turns` (не cold start).
2. **Multi-tenant** → per-ticket `session_id` + `resume` (не `continue`).
3. **JWT vs OAuth2** → `resume` + `fork_session=True`; оригинал отдельно.
4. **Форк правил файл** → правки реальны в cwd; нужен checkpointing / VCS.
5. **Ephemeral disk** → заранее `SessionStore` / jsonl, либо case-facts в новую сессию.

Источник: code.claude.com/docs/en/agent-sdk/sessions
