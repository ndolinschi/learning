# Rate Limiting в распределённых системах: Token Bucket, Sliding Window, Redis

**Дата:** 2026-09-15  
**Трек:** System Design  
**Язык:** русский  
**Тип документа:** исследовательский бриф (offline backup)

---

## 1. Краткое резюме

Rate limiting (ограничение частоты запросов) — механизм, который ограничивает, сколько запросов клиент (пользователь, API-ключ, IP, тенант) может сделать за заданный интервал времени. В распределённых системах локальные счётчики на каждом узле **недостаточны**: без общего состояния лимит «10 RPS» на 30 gateway-нодах фактически превращается в ~300 RPS. Поэтому в продакшене почти всегда нужен **общий store** (чаще всего Redis) и **атомарные** операции (Lua/`EVAL`), чтобы исключить гонки TOCTOU.

Пять базовых алгоритмов покрывают почти все кейсы:

| Алгоритм | Память | Точность | Всплески (burst) | Типичное применение |
|---|---|---|---|---|
| Fixed Window | O(1) | приближённая | до ~2× на границе окна | простые лимиты, login throttle |
| Sliding Window Log | O(n) | точная | без «дыр» на границе | критичные API, аудит |
| Sliding Window Counter | O(1) | почти точная | сглаженные границы | **дефолт для публичных API** |
| Token Bucket | O(1) | точная | контролируемые burst | SDK, мобильные клиенты |
| Leaky Bucket | O(1)–O(queue) | точная | строгий drain / shaping | защита хрупкого downstream |

**Практический дефолт:** Sliding Window Counter в Redis + Lua; Token Bucket — если burst нужен как фича; Leaky Bucket (policing/shaping) — если burst опасен для downstream.

---

## 2. Зачем это важно (Why it matters)

1. **Защита инфраструктуры** — один агрессивный клиент или бот не должен исчерпать CPU, connection pool, БД или квоту стороннего API.
2. **Fairness / multi-tenancy** — равный доступ к shared-ресурсу между тенантами и тарифами.
3. **Контроль стоимости** — LLM, SMS, платежные шлюзы, map APIs тарифицируются за вызов.
4. **Стабильность UX** — лучше предсказуемый `429 + Retry-After`, чем каскадный отказ всего кластера.
5. **Compliance и abuse prevention** — brute-force логина, credential stuffing, scraping.
6. **Слоистая защита** — CDN (грубо по IP) → Gateway (API-key/route) → Service (бизнес-лимиты) → Downstream circuit breakers.

Без координации между узлами «лимит» — иллюзия. Это главная системная проблема, а не выбор формулы счётчика.

---

## 3. Алгоритмы и trade-offs

### 3.1. Fixed Window (фиксированное окно)

**Идея:** время делится на непересекающиеся окна (например, минуты). В каждом окне — счётчик. Превысил лимит → отказ до начала следующего окна.

**Redis:** `STRING` + `INCR` + `EXPIRE` (обычно в Lua).

```lua
local count = redis.call('INCR', key)
if count == 1 then
  redis.call('EXPIRE', key, window_seconds)
end
return { count, redis.call('PTTL', key) }
```

**Плюсы:** минимальная память, простота, скорость.  
**Минусы:** **boundary burst** — клиент может сделать N запросов в конце окна и ещё N в начале следующего → до **2N** за короткий интервал.

**Когда уместен:** login attempts, внутренние сервисы, где приближение допустимо.

---

### 3.2. Sliding Window Log (лог скользящего окна)

**Идея:** хранить timestamp каждого запроса. На новой попытке удалить устаревшие, посчитать оставшиеся, при необходимости добавить новую запись.

**Redis:** `ZSET` (score = timestamp, member = unique id) + Lua: `ZREMRANGEBYSCORE` → `ZCARD` → `ZADD` → `EXPIRE`.

**Плюсы:** истинное скользящее окно, нет boundary burst, точный `Retry-After` от самого старого события.  
**Минусы:** память **O(число запросов в окне)** на клиента; дорого при высоких лимитах (10k req/hour × миллионы клиентов).

**Когда уместен:** платежи, auth, аудит, низкий/средний RPS на ключ.

---

### 3.3. Sliding Window Counter (гибрид / floating window)

**Идея:** два fixed-window счётчика (текущее + предыдущее окно). Оценка:

\[
\text{estimated} = \text{prev} \times (1 - \text{elapsed}) + \text{curr}
\]

где `elapsed ∈ [0,1]` — доля текущего окна, уже прошедшая.

**Redis:** два `STRING` ключа + Lua; в Cluster — **hash tags** `{userId}:windowNum`, чтобы оба ключа попали в один slot.

**Плюсы:** почти точность log-варианта при O(1) памяти; сглаживает boundary burst.  
**Минусы:** оценка (не аудит timestamp'ов); Tiny ошибка обычно приемлема (в индустрии часто цитируют ~0.003% на масштабе Cloudflare-подобных систем для близких схем).

**Когда уместен:** **лучший дефолт** для публичных API и gateway middleware.

---

### 3.4. Token Bucket (корзина токенов)

**Идея:** токены наполняются с постоянной скоростью до `capacity`. Каждый запрос тратит ≥1 токен. Пустая корзина → отказ. Burst разрешён до ёмкости корзины.

**Redis:** `HASH` с полями `tokens`, `last_refill` + Lua (refill → check → consume).

**Параметры:**
- `capacity` (burst size)
- `refill_rate` (токены/сек) → долгосрочный average rate

**Плюсы:** controlled bursts + строгий average; естественен для SDK/мобильных клиентов.  
**Минусы:** burst может ударить по fragile downstream даже при «честном» average; floating-point refill требует аккуратности.

**Дуал с Leaky Bucket:** Token Bucket стартует полной и «тратит»; Leaky Bucket стартует пустой и «наполняется» запросами.

---

### 3.5. Leaky Bucket (дырявое ведро)

Два режима:

1. **Policing (meter):** виртуальный уровень заполнения; overflow → **немедленный отказ** (без задержки).
2. **Shaping (queue):** запросы ставятся в очередь и выпускаются с фиксированной скоростью; при переполнении очереди — drop; accepted запросы получают **delay**.

**Redis:** `HASH` (`level`/`last_leak` или `next_free`) + Lua.

**Плюсы:** строгий выходной rate; защита downstream от spikes.  
**Минусы:** shaping добавляет latency; для синхронных HTTP API часто проще policing + `429`.

**Выбор:** downstream выдерживает краткие всплески → Token Bucket; не выдерживает → Leaky Bucket.

---

### 3.6. Слойные лимиты (рекомендация)

В продакшене часто комбинируют:
- **глобальный** fixed/sliding window (защита кластера);
- **per-user Token Bucket** (удобство клиента);
- **per-route** более жёсткий лимит на дорогие эндпоинты (`/search`, `/export`, LLM).

---

## 4. Паттерны Redis для распределённого rate limiting

### 4.1. Почему Redis

- In-memory latency (микросекунды–миллисекунды).
- Атомарный `INCR`, TTL/`EXPIRE`, богатые структуры (`HASH`, `ZSET`).
- `EVAL` Lua: read–decide–write без гонок и без retry-шторма `WATCH`.

### 4.2. INCR + EXPIRE (Fixed Window)

Классика: ключ `rl:{id}:{windowId}`, `INCR`, при первом запросе окна — `EXPIRE`.  
**Критично:** `INCR` и `EXPIRE` должны быть атомарны (Lua), иначе crash между ними оставляет ключ **без TTL** → вечная блокировка клиента.

### 4.3. Lua / EVAL — стандарт атомарности

| Подход | Подходит? | Почему |
|---|---|---|
| Отдельные команды | Нет | TOCTOU race |
| `MULTI`/`EXEC` | Обычно нет | нельзя ветвиться по прочитанному |
| `WATCH`+`MULTI` | Плохо под нагрузкой | aborts → retries именно когда лимитер нужнее всего |
| **Lua `EVAL`** | **Да** | атомарно, один round-trip, ветвление |

Ограничения Lua: скрипт блокирует event loop → держать коротким; в Cluster все `KEYS` — один hash slot.

### 4.4. ZSET Sliding Log

```text
ZREMRANGEBYSCORE key -inf (now - window)
ZCARD key
ZADD key now uniqueMember   -- если count < limit
EXPIRE key window+buffer
```

Members должны быть уникальны (`timestamp:uuid`), иначе коллизии в одну миллисекунду.

### 4.5. Sliding Window Counter на двух ключах

```text
estimated = prev * (1 - elapsed) + curr
if estimated >= limit → deny
else INCR current; EXPIRE current (2 * window)
```

Hash tags: `{rl:user42}:17123` и `{rl:user42}:17122`.

### 4.6. Token / Leaky на HASH

Хранить состояние (`tokens`/`last_refill` или `level`/`last_leak`), обновлять в одном Lua-скрипте, TTL ≈ время полного refill/drain + запас.

### 4.7. Кластер, репликация, отказоустойчивость

- **Cluster:** hash tags для multi-key скриптов.
- **Fail-open vs fail-closed:** при недоступности Redis решить политику (часто fail-open для read-heavy публичных API с edge-лимитами; fail-closed для auth/payment).
- **Clock skew:** для Token/Leaky timestamp лучше передавать из приложения согласованно; для window-счётчиков — epoch секунд.
- **Кардинальность ключей:** отдельный ключ на `(scope, identifier, window)` — следить за memory и eviction policy (`volatile-ttl` / отсутствие random eviction критичных ключей).

---

## 5. Размещение (Placement)

Многослойная модель:

```text
Client
  │
  ▼
CDN / Edge  ──── грубый per-IP / volumetric abuse (Cloudflare, Fastly, CloudFront)
  │
  ▼
API Gateway ─── per-API-key, per-route, tenant tier (Kong, Envoy, AWS API GW, Nginx)
  │
  ▼
Application ─── бизнес-лимиты (user, feature, cost-based)
  │
  ▼
Downstream ──── circuit breaker / bulkhead на зависимости
```

**Принципы:**
1. Отсекать мусор **как можно раньше** (дешевле на edge).
2. На edge — грубые IP-лимиты; осторожно с NAT/корпоративными прокси (ложные блоки).
3. После аутентификации — лимит по стабильному identity (user/API-key), не только IP.
4. Синхронизировать слои: gateway 1000/min при service 100/min путает клиентов.
5. Health-check и static assets обычно исключают из лимитера.

---

## 6. HTTP-семантика: 429, Retry-After, заголовки

### 6.1. RFC 6585 — `429 Too Many Requests`

- Статус означает: клиент отправил слишком много запросов за период.
- Тело **SHOULD** объяснять условие.
- `Retry-After` **MAY** указывать, сколько ждать (секунды или HTTP-date).
- Ответы `429` **MUST NOT** кэшироваться.
- При DDoS сервер **не обязан** отвечать 429 на каждый запрос (можно дропать соединения) — см. security considerations RFC.

### 6.2. Практические заголовки (de-facto)

Часто встречаются (не единый стандарт):
- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`
- `Retry-After`

Их стоит отдавать **и на успешных** ответах, чтобы клиенты самотроттлились.

### 6.3. IETF draft `RateLimit` / `RateLimit-Policy`

Черновик `draft-ietf-httpapi-ratelimit-headers` определяет:
- `RateLimit-Policy` — политика квоты (`q`, `w`, …);
- `RateLimit` — текущее состояние (`r` remaining, `t` effective window, …).

Если есть и `Retry-After`, и `RateLimit`, **приоритет у `Retry-After`**.

### 6.4. Поведение клиента

1. Уважать `Retry-After`.
2. Exponential backoff + **jitter** (иначе thundering herd).
3. Не ретраить мгновенно параллельными воркерами «в обход».
4. Различать `429` (можно повторить позже) и `403`/`401` (права/аутентификация).

Пример ответа:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 42
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1735689600
Content-Type: application/problem+json

{
  "type": "https://api.example.com/errors/rate-limited",
  "title": "Rate limit exceeded",
  "status": 429,
  "detail": "Превышен лимит 1000 запросов/час на /v2/search",
  "retry_after_seconds": 42
}
```

---

## 7. Подводные камни (Pitfalls)

1. **Локальный лимитер на каждом поде** без Redis → эффективный лимит × N реплик.
2. **Гонки без Lua** → обход лимита под concurrency.
3. **Ключ без TTL** после частичного сбоя → вечный бан.
4. **Boundary burst Fixed Window** недооценён → 2× нагрузка на БД.
5. **Token Bucket burst** убивает downstream connection pool.
6. **Sliding Log memory explosion** при высоких лимитах и высокой кардинальности.
7. **IP-only лимиты** бьют NAT/CGNAT и мобильные сети; пропускают распределённые боты.
8. **Несогласованные лимиты** между CDN/Gateway/App.
9. **Thundering herd** после массового `429` без jitter.
10. **Redis single point / hot key** на популярном глобальном ключе — шардировать scope или использовать local+global иерархию.
11. **Clock skew** между нодами при Token/Leaky refill.
12. **Считать rejected запросы в счётчик** (или наоборот) — зафиксировать политику явно.
13. **Отсутствие заголовков** → клиенты «долбят» вслепую.
14. **Fail-closed без деградации** при падении Redis → полный outage API.
15. **Лимит на health/metrics** → ложные алерты и убитый probes.

---

## 8. Чеклист takeaways

- [ ] Понял разницу Token Bucket (burst как фича) vs Leaky Bucket (burst как угроза).
- [ ] Знаю failure mode Fixed Window: до 2N на границе.
- [ ] Sliding Window Counter — практичный дефолт (O(1), почти точный).
- [ ] Sliding Window Log — точный, но O(n) по памяти.
- [ ] В Redis для RMW всегда Lua/`EVAL`, не голый pipeline.
- [ ] `INCR`+`EXPIRE` атомарно; иначе риск ключа без TTL.
- [ ] Cluster: hash tags для multi-key скриптов.
- [ ] Размещение: Edge → Gateway → Service (отсекай рано).
- [ ] HTTP: `429` + `Retry-After` + informative headers/body.
- [ ] Клиенты: backoff + jitter; уважать Retry-After.
- [ ] Политика fail-open/fail-closed при недоступности store осознанна.
- [ ] Identity для лимита: user/API-key предпочтительнее голого IP после auth.
- [ ] Мониторинг: reject rate, Redis latency/errors, hot keys, false positives.
- [ ] Документировать лимиты публично (developer experience).

---

## 9. Курируемые ссылки

### Нормативные / стандарты
1. [RFC 6585 — 429 Too Many Requests](https://httpwg.org/specs/rfc6585.html)  
2. [IETF draft-ietf-httpapi-ratelimit-headers](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-ratelimit-headers-11)  

### Redis и алгоритмы
3. [Redis tutorial: Build 5 Rate Limiters](https://redis.io/tutorials/howtos/ratelimiting/) — эталонные Lua-паттерны для всех пяти алгоритмов  
4. [Redis developer repo: redis-ratelimiting-js](https://github.com/redis-developer/redis-ratelimiting-js)  
5. [OneUptime: Sliding Window with Redis](https://oneuptime.com/blog/post/2026-03-31-redis-how-to-implement-sliding-window-rate-limiting-with-redis/view)  

### System Design / сравнение
6. [API Rate Limiting Strategies: 2026 Engineering Reference](https://www.digitalapplied.com/blog/api-rate-limiting-strategies-2026-engineering-reference)  
7. [Rate Limiting: how Redis, Cloudflare, Stripe, and Envoy do it](https://iam.slys.dev/p/rate-limiting-how-redis-cloudflare)  
8. [Deep comparison of 4 algorithms (experiments)](https://yuhi-sa.github.io/en/posts/20260720_rate_limiting/1/)  
9. [FlowVerify: which of the four algorithms to use](https://www.flowverify.co/blog/rate-limiting-production-four-algorithms)  
10. [DEV: System Design Rate Limiter](https://dev.to/rhuturaj_takle/system-design-rate-limiter-38b3)  

### Placement и HTTP-практика
11. [What Is API Rate Limiting? (429 handling, layers)](https://datacelix.com/what-is-api-rate-limiting/)  
12. [Rate Limiting at the Edge: CDN and API Gateway](https://wittycoder.in/courses/rate-limiting/rate-limiting-edge)  
13. [Rate Limiting in Your Architecture](https://wittycoder.in/courses/rate-limiting/rate-limiting-architecture)  

### Дополнительно
14. [arXiv: Designing Scalable Rate Limiting Systems](https://arxiv.org/html/2602.11741)  
15. [Level Up Coding: Production Rate Limiter in Go + Redis + Lua](https://levelup.gitconnected.com/building-a-production-ready-rate-limiter-in-go-with-redis-and-lua-616d79873418)  

---

## 10. Мета

- Тема дня: Rate Limiting в распределённых системах  
- Трек: System Design  
- Назначение файла: offline research backup (параллельно browser Claude research)  
- Не пушить в GitHub из этого шага  
