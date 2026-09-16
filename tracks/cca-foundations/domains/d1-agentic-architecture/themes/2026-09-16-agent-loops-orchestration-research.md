# Агентные циклы и оркестрация: stop_reason, координатор и субагенты, явная передача состояния

**Трек:** Claude Certified Architect – Foundations (CCA-F)  
**Домен:** D1 Agentic Architecture & Orchestration (вес ~27%, независимые гайды)  
**Дата:** 2026-09-16 (утренний слот, Europe/Chisinau)  
**Тип:** deep research для экзамена (не официальный blueprint Anthropic)  
**Источники:** docs/platform.claude.com (stop_reason, tool use), Anthropic Engineering (Building Effective Agents; Multi-Agent Research System), Managed Agents multiagent docs, exam-reality-brief + публичные сценарии ClaudeArchitectCertification

---

## 1. Зачем эта тема для CCA-F

D1 — самый тяжёлый домен в публичных независимых разборах (~27%). Вопросы — архитектурное суждение: кто владеет контролем и состоянием, какой слой **детерминированно** закрывает требование, почему «усилить промпт» — дистрактор.

Канонические сценарии, к которым привязана тема:
- **Multi-Agent Research System** — hub-and-spoke, семантическая декомпозиция, изолированные исследователи, синтез с противоречиями.
- **Customer Support Resolution Agent** — цикл по `stop_reason`, case-facts, эскалация человеку, ограниченные ретраи.

---

## 2. Workflow vs Agent

По Anthropic (*Building Effective Agents*):

| | **Workflow** | **Agent** |
|---|---|---|
| Управление | Код заранее задаёт путь (цепочка, routing, parallel, orchestrator-workers с фиксированной топологией) | LLM динамически выбирает шаги и инструменты в цикле |
| Когда | Подзадачи предсказуемы, нужна предсказуемость и стоимость | Число шагов неизвестно, нужна гибкость |
| Цена | Ниже latency/cost, проще аудит | Выше cost/latency, риск накопления ошибок |
| Контроль | Gate/валидация в коде между шагами | Бюджеты итераций, таймауты, HITL, scoped tools |

**Правило экзамена:** начинать с самого простого. Агент — когда нельзя надёжно захардкодить путь. Workflow — когда путь известен (извлечение → валидация → запись).

Оркестратор–workers близок к multi-agent, но в чистом workflow подзадачи всё ещё «в рамках паттерна»; у полноценного агента lead сам решает, сколько субагентов и с каким scope.

---

## 3. Agent loop и Messages API `stop_reason`

Канонический цикл **не парсит свободный текст** («сейчас вызову инструмент…»). Ветка — по структурированному полю `stop_reason` ответа Messages API.

### 3.1. Ключевые значения (официальная шпаргалка)

| `stop_reason` | Смысл | Действие приложения |
|---|---|---|
| `end_turn` | Нормальное завершение | Взять ответ как финал (осторожно с пустыми ответами после tool_result) |
| `tool_use` | Нужно выполнить client tool(s) | Выполнить tools → вернуть **только** `tool_result` блоки |
| `max_tokens` | Упёрлись в лимит | Поднять `max_tokens` / продолжить; если обрублен `tool_use` — retry с большим лимитом |
| `stop_sequence` | Сработала stop_sequence | Прочитать `stop_sequence` |
| `pause_turn` | Server-tool loop достиг лимита итераций | Переслать assistant content as-is (без user «continue»), с теми же tools |
| `refusal` | Отказ safety | Смотреть `stop_details`, fallback модель/переформулировка |
| `model_context_window_exceeded` | Упёрлись в окно контекста | Считать ответ усечённым; компакция / durable facts / новый ход |

### 3.2. Псевдоцикл

```text
messages = [user]
while True:
  response = messages.create(..., tools=tools, messages=messages)
  if response.stop_reason == "tool_use":
      results = run_client_tools(response.content)  # таймауты, isError
      messages += [assistant(response.content), user(tool_results_only)]
      continue
  if response.stop_reason == "pause_turn":
      messages += [assistant(response.content)]  # last message = paused assistant
      continue
  if response.stop_reason == "max_tokens":
      handle_truncation_or_continue()
      ...
  # end_turn / refusal / ...
  break
```

### 3.3. Жёсткие инварианты tool_use

1. После `tool_use` user-сообщение содержит **только** `tool_result` (без текста «вот результат») — иначе Claude учится ждать текст и может давать пустой `end_turn`.
2. Массив `tools` на продолжении тот же (иначе 400 на pending server tool).
3. Не завершать успешно на пустом `[]` от tool timeout — возвращать **структурированную ошибку** (`isError: true`, класс: timeout / permission / retryable).
4. Бюджет итераций внешнего цикла обязателен (max steps), иначе runaway cost.

**Экзаменный дистрактор:** «парсить текст ответа и искать имя инструмента» — неправильно. Правильно — `stop_reason == tool_use` + блоки `tool_use`.

---

## 4. Координатор (hub-and-spoke) vs peer-to-peer

### 4.1. Hub-and-spoke (предпочтительный паттерн для CCA-F)

- **Координатор (lead)** владеет целью, декомпозицией, маршрутизацией, синтезом, эскалацией.
- **Субагенты** — узкий scope: свой system prompt, свой список tools, своя история.
- Маршрутизация **через координатора**, не прямой вызов research_A → research_B.

Почему не peer-to-peer на экзамене:
- теряется единая политика (бюджеты, audit, отказ);
- сложнее воспроизводимость и отладка;
- легко получить дубликаты работы и «дыры» в покрытии темы;
- нет единой точки синтеза противоречий.

Anthropic Research system и Managed Agents multiagent: lead делегирует, субагенты в **изолированных thread/context**; shared может быть filesystem/sandbox, но **не** история диалога и не полный tool surface координатора.

### 4.2. Managed Agents (лёгкий cross-cut)

- Roster на координаторе (`multiagent.agents`), глубина делегирования обычно 1 уровень.
- У каждого агента свои model / prompt / tools / MCP / skills.
- Не предполагать shared conversation history.

Для CCA-F важнее принцип изоляции и явной передачи, чем заучивание полей SDK.

---

## 5. Семантическая декомпозиция до spawn

Типичный провал сценария Research: отчёт «про искусства» покрыл только visual arts, потому что координатор плохо разбил задачу.

Правила:
1. Сначала **семантика задачи** (какие независимые оси/домены нужны), потом spawn.
2. 3–5 параллельных исследователей с **непересекающимися** brief'ами лучше, чем один «исследуй всё».
3. В задании субагенту: objective, границы, источники/tools, формат выхода, что **не** делать.
4. Короткое «research X» без границ → дубликаты и пробелы (урок из Anthropic multi-agent research post).

**Экзамен:** отчёт пропустил music/writing → чинить **декомпозицию координатора**, не temperature синтезатора и не промпт одного исследователя.

---

## 6. Явные task prompts + scoped tools; нет наследования истории

Критический инвариант:
> Субагент **не наследует** историю координатора, пока факты не переданы явно в assignment (или через согласованный артефакт: файл, case-facts store).

Практика:
- Передавать `customer_id`, сумму, политику, уже проверенные факты в тексте задания.
- Давать **минимальный** tool list (least privilege): refund-субагенту не нужен web_search; research-субагенту — read-only search, без `issue_refund`.
- Не просить субагента «догадаться» недостающие факты из невидимого контекста.

Связь с Customer Support: субагент refund fails eligibility → обычно не «усилить system prompt», а **передать контекст явно + сузить tools**.

---

## 7. State handoff, persistence, durable facts

Долгоживущий агент сжимает/суммирует контекст → теряет `customer_id`, сумму refund, карту.

Паттерн **durable / case-facts**:
- структурированный блок фактов дела вне «болтливой» истории;
- перечитывать/подмешивать каждый ход (или после compaction);
- обновлять только через явные операции (verify identity → set fact).

Handoff между координатором и субагентом = тот же принцип: контракт входа/выхода (JSON schema результата), а не «продолжи разговор».

Persistence между сессиями: store case id → facts → audit log tool calls. После краша — resume с facts, не с пустого чата.

Связь с D5 (Context Management): эта тема пересекается, но в D1 акцент на **кто владеет state и как передаёт**.

---

## 8. Bounded retries, timeouts, escalation / HITL

### 8.1. Ошибки tools

| Плохо | Хорошо |
|---|---|
| Timeout → `[]` как success | `{ isError: true, class: "timeout", retryable: true }` |
| Permission denied → бесконечный retry | Классифицировать non-retryable, эскалировать |
| «Попробуй ещё раз» в промпте | Бюджет retry в коде + backoff + circuit |

### 8.2. Бюджеты

- max agent steps / max tool calls / wall-clock timeout;
- max parallel subagents;
- max digests/continuations для `pause_turn`.

### 8.3. Human-in-the-loop

Эскалация при:
- низкая confidence / негативный sentiment (support);
- действие выше порога (refund cap — лучше **детерминированный** PreToolUse/hook, не только промпт);
- противоречивые источники без политики автовыбора;
- исчерпан retry budget.

HITL — first-class путь, не afterthought.

---

## 9. Связка со сценариями экзамена

### Multi-Agent Research System
- Hub-and-spoke coordinator.
- Semantic decomposition → 3–5 isolated researchers.
- Verification/synthesis сохраняет conflicting claims + attribution.
- Structured timeout errors, не silent empty.
- Read-only synthesis stage.

### Customer Support Resolution Agent
- Loop на `stop_reason`.
- Identity verification before account mutations.
- Case-facts block.
- PreToolUse refund cap (deterministic).
- Sentiment/confidence → escalate human.
- Audit log.

---

## 10. Чеклист архитектурного решения (для MCQ)

1. Требование **binding**? (cap, identity, schema) → детерминированный слой.
2. Путь шагов известен? → workflow; иначе agent loop по `stop_reason`.
3. Нужна специализация/параллель? → coordinator + scoped subagents.
4. Контекст субагенту передан явно?
5. Есть бюджеты, классификация ошибок, HITL?
6. Durable facts переживают summarization?

---

## 11. Практические вопросы (practice, не официальные Anthropic)

См. `opisatelnyj-doc.md` — 5 вопросов с краткими ответами, парафраз публичных тем.

---

## 12. Источники

1. https://platform.claude.com/docs/en/build-with-claude/handling-stop-reasons  
2. https://platform.claude.com/docs/en/agents-and-tools/tool-use/how-tool-use-works  
3. https://www.anthropic.com/engineering/building-effective-agents  
4. https://www.anthropic.com/engineering/multi-agent-research-system  
5. https://platform.claude.com/docs/en/managed-agents/multiagent-orchestration  
6. /workspace/cca-f-research/exam-reality-brief.md  
7. Публичные сценарии: claudearchitectcertification.com (Multi-Agent Research; Customer Support) — независимый framing, не официальный blueprint.

**Оговорка:** веса доменов и формат 60Q/120min/720 — из независимых гайдов; Anthropic публично не публикует scored blueprint.
