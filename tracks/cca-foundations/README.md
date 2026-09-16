# Claude Certified Architect – Foundations (CCA-F)

Техническая сертификация Anthropic для архитекторов production-решений на Claude (не «промптинг ради промптинга»).

> **Важно:** Anthropic подтверждает существование CCA-F и экосистему обучения, но **не публикует официальный scored blueprint** (веса доменов, банк вопросов, raw→720). Формат, веса и task statements ниже — из независимых гайдов и research brief (2026-09-15). Сверять с Skilljar / candidate terms.

## Обзор формата (независимые источники)

| Параметр | Публичные отчёты | Оговорка |
|----------|------------------|----------|
| Вопросы | ~60 scenario-based | независимые гайды |
| Время | ~120 минут | независимые гайды |
| Проход | 720 / 1000 (scaled) | независимые гайды |
| Типы | single-choice и/или multiple-response | версии расходятся |
| Доставка | closed-book, online-proctored (часто указывают Pearson VUE) | vendor/fee могут меняться |
| Сценарии | 4 из 6 «канонических» на сидящем (по некоторым гайдам) | не официальная гарантия |

Ссылка на покрытие: [`coverage.md`](coverage.md)

## Домены и веса (независимый синтез)

| ID | Домен | Вес |
|----|--------|----:|
| D1 | Agentic Architecture & Orchestration | **27%** |
| D2 | Tool Design & MCP Integration | **18%** |
| D3 | Claude Code Configuration & Workflows | **20%** |
| D4 | Prompt Engineering & Structured Output | **20%** |
| D5 | Context Management & Reliability | **15%** |

Карты задач:

- [D1](domains/d1-agentic-architecture/map.md)
- [D2](domains/d2-tools-mcp/map.md)
- [D3](domains/d3-claude-code/map.md)
- [D4](domains/d4-prompt-structured/map.md)
- [D5](domains/d5-context-reliability/map.md)

## Канонические сценарии (study proxies)

1. Customer Support Resolution Agent  
2. Code Generation with Claude Code  
3. Multi-Agent Research System  
4. Developer Productivity Tools  
5. Claude Code in CI/CD  
6. Structured Data Extraction  

## Вне скоупа (не тратить время)

По независимым гайдам **не** в экзамене:

- fine-tuning / обучение своих моделей  
- API auth, billing, OAuth, ротация ключей  
- хостинг/инфра MCP (сеть, контейнеры)  
- внутренняя архитектура Claude, веса, Constitutional AI / RLHF  
- embeddings / vector DB  
- computer use, vision  
- streaming SSE, rate limits / pricing math  
- cloud provider config, бенчмарки моделей  
- детали реализации prompt caching, алгоритмы токенизации  

## Навигация

| Файл | Назначение |
|------|------------|
| [`coverage.md`](coverage.md) | % покрытия (взвешенный) |
| [`backlog.md`](backlog.md) | приоритизированный backlog |
| [`sources.md`](sources.md) | источники |
| [`practice/README.md`](practice/README.md) | заметки по практике |
