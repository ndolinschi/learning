# Rate Limiting в распределённых системах: Token Bucket, Sliding Window, Redis

- **Трек:** System Design
- **Дата:** 2026-09-15
- **Статус:** ✅ изучено (ежедневный drop)
- **Notebook:** https://notebook.google.com/notebook/713a9614-10d2-4146-8ce9-5b45c80b0d85
- **Claude chat:** недоступен (monthly spend limit 2026-09-15); research из локального brief

## Кратко

Rate limiting ограничивает частоту запросов на ключ (IP, user, API key), чтобы защитить сервисы от перегрузки и злоупотреблений. В распределённой среде счётчики обычно держат в Redis; выбор алгоритма (Token Bucket, Sliding Window и др.) влияет на burst-поведение, память и справедливость.

## Ключевые идеи

1. Fixed Window прост, но даёт boundary burst; Sliding Window / Token Bucket сглаживают пики лучше.
2. Redis: INCR+EXPIRE, Lua-скрипты, ZSET для sliding log — разные tradeoff по точности и стоимости.
3. Размещайте лимиты на edge/gateway и дублируйте на сервисе для defense in depth.
4. HTTP: 429, Retry-After, заголовки RateLimit-* — контракт для клиентов.
5. Типичные ошибки: локальные in-memory лимиты на multi-instance, рассинхрон часов, слишком грубый ключ.

## Материалы

- Презентация и аудио/видео: в notebook выше
- Research / doc: локальный drop `learning-2026-09-15` на компьютере Learning Bot

## Источники

Redis rate limiting tutorials; RFC 6585 (429); IETF RateLimit headers draft; Stripe/Cloudflare/Google architecture notes — см. research brief.
