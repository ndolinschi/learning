# Virtual Threads в Java 21+: Research Brief

**Версия**: Java 21 LTS, Java 22–23 (preview features)  
**Дата**: сентябрь 2026  
**Уровень**: Senior Java Engineer

---

## 1. Краткое резюме

Virtual Threads (VT, или виртуальные потоки) — это в Java 21 LTS завершённая функция, которая кардинально меняет модель конкурентности на JVM. Вместо парадигмы thread-per-request с ограниченным числом platform threads (~10K–100K), разработчики теперь могут создавать миллионы лёгких virtual threads, каждый занимая ~1 КБ памяти вместо ~2 МБ.

Главное достижение: **virtual threads позволяют масштабировать I/O-интенсивные приложения без переписывания на async/await или реактивные фреймворки**. Код остаётся синхронным и привычным, а JVM сама управляет распределением работы на небольшом пуле carrier threads (platform threads).

**Критическое предостережение**: Virtual threads требуют осторожного обращения с синхронизацией, native-кодом и управлением ресурсами. Неправильное использование (особенно pinning — блокировка carrier thread) мгновенно нейтрализует их преимущества.

---

## 2. Virtual Threads vs Platform Threads: модель памяти и отличия

### 2.1 Platform Threads (традиционные потоки ОС)

**Platform thread** — это 1:1-отображение на поток операционной системы:
- **Размер стека**: ~2 МБ на x86_64 (минимум 1 МБ)
- **Управление**: ОС через scheduler, context switching дорогой
- **Лимит**: типично 10K–100K потоков на сервере (зависит от памяти)
- **Переключение**: ~1–10 микросекунд, плюс cache invalidation

Модель памяти:
```
[Heap: Java objects]
       ↓
[Thread stacks: 2 МБ × N потоков]  ← быстро исчерпывается
       ↓
[Native memory: JVM structs, JNI]
```

### 2.2 Virtual Threads (Java 21+)

**Virtual thread** — это поток JVM, управляемый ForkJoinPool (по умолчанию), выполняется на carrier thread:
- **Размер стека**: ~1 КБ (динамический, растёт по мере надобности)
- **Управление**: JVM scheduler, переключение ~наносекунда
- **Лимит**: миллионы (тестировано >10M на ноутбуке)
- **Переключение**: вытеснение без context switching ОС

Модель памяти:
```
[Heap: Java objects + Virtual Thread objects ~1 КБ each]
       ↓
[Carrier thread stacks: ~2 МБ × (ядра × K-factor)]  ← фиксировано
       ↓
[Native memory: JVM structs]
```

### 2.3 Таблица сравнения

| Аспект | Platform Thread | Virtual Thread |
|--------|-----------------|----------------|
| Память на поток | ~2 МБ | ~1 КБ |
| Создание | миллисекунды | микросекунды |
| Макс. потоков на сервер | 10K–100K | 1M–10M+ |
| Switching cost (ОС) | высокая | отсутствует |
| Управление | ОС kernel | JVM ForkJoinPool |
| Блокирующий I/O | блокирует ядро | yield & mount на другой carrier |
| Синхронизация | работает, но дорого | нужна осторожность (pinning) |

**Факт (JEP 444)**: Virtual threads не замену платформенные потоки везде. Они специально для I/O-bound, а не CPU-bound рабочих нагрузок.

---

## 3. Carrier Threads и планировщик: mount, unmount, work stealing

### 3.1 Основная идея

Один **carrier thread** (platform thread) выполняет несколько virtual threads поочередно:

```
Создано:      10 000 virtual threads
Carrier pool: 8–16 platform threads (примерно на ядро)
Ratio:        1000:1 мультиплексирование
```

### 3.2 Жизненный цикл Virtual Thread

```
[1] new Thread.ofVirtual().start(task)
        ↓
[2] UNMOUNTED (ждёт очереди на carrier)
        ↓
[3] MOUNTED на carrier, выполняется
        ↓
[4] Упирается в блокирующий вызов (Socket.read(), ...)
        ↓
[5] UNMOUNTED (освобождает carrier, поток продолжает ждать)
        ↓
[6] Операция завершена → готов выполняться
        ↓
[7] MOUNTED снова, выполняется
        ↓
[8] Завершение или исключение
```

### 3.3 ForkJoinPool Scheduler

Virtual threads используют **shared ForkJoinPool**, настраиваемый через:
```bash
-Djdk.virtualThreadScheduler.parallelism=N       # default: Runtime.getRuntime().availableProcessors()
-Djdk.virtualThreadScheduler.maxPoolSize=N       # по умолчанию: parallelism + 256
-Djdk.virtualThreadScheduler.minRunqueues=N      # распределение очередей
```

**Work-stealing queue**: каждый carrier thread имеет свою очередь. Если одна очередь пуста, carrier может "украсть" работу у другого — это балансирует нагрузку.

### 3.4 Ключевое уточнение

**Virtual thread не "паузится" сам по себе**. Он может быть unmounted только при блокирующем вызове, который JVM знает о нём:
- Socket I/O (java.net, java.nio — work с VT, но не java.nio.channels.FileChannel на локальный диск)
- Object.wait(), ReentrantLock (новая поддержка в Java 21)
- Thread.sleep()
- Synchronized блок → **PINNING** (см. раздел 4)

Если код зависает в вычислениях, virtual thread будет занимать carrier полностью.

---

## 4. Pinning: диагностика, исправление, deep dive

### 4.1 Что такое Pinning

**Pinning** — ситуация, когда virtual thread не может быть unmounted, потому что держит ресурс, который не может быть перенесен:

```
Virtual thread пытается执行 → упирается в блокирующий вызов
                           ↓
                   JVM проверяет: может ли unmount?
                           ↓
                   НЕТ (pinned) ← carrier тоже блокируется
                           ↓
                   Другие VT не могут использовать этот carrier
                           ↓
                   Горячее место становится узким местом
```

### 4.2 Причины Pinning

#### Случай 1: `synchronized` блок
```java
synchronized (this) {  // ❌ PINNING: монитор держится на carrier
    socketInputStream.read();  // блокирующий вызов
}
```

**Почему**: Java monitor на уровне C++ в HotSpot неразрывно связана с потоком. Если virtual thread держит монитор, JVM не может его safely unmount.

**Диагностика JFR**:
```
Event: jdk.VirtualThreadPinned
Message: Virtual thread pinned for 100 ms (synchronized block)
Stack trace: YourClass.badMethod() [synchronized]
```

#### Случай 2: Native методы (JNI)
```java
private native void nativeBlockingCall();  // ❌ PINNING гарантирован

void foo() {
    nativeBlockingCall();  // JVM не может unmount в native коде
}
```

**Почему**: JVM не может приостановить виртуальный поток внутри native кода — нет гарантии, что native функция supportит такие прерывания.

#### Случай 3: Foreign Function & Memory API (Java 19+, preview)
```java
Arena arena = Arena.ofConfined();  // ❌ Confined arena pinning
MemorySegment seg = ..;
foreignCall(seg);  // может pinned'ить
```

**Контекст**: Scoped values и arena management в Java 21+ улучшили ситуацию, но полной гарантии нет.

### 4.3 Исправление Pinning

**Стратегия 1: ReentrantLock вместо synchronized**
```java
private final ReentrantLock lock = new ReentrantLock();

void goodMethod() {
    lock.lock();
    try {
        // ✅ Virtual thread может быть unmounted здесь
        socketInputStream.read();
    } finally {
        lock.unlock();
    }
}
```

**Почему работает**: ReentrantLock в Java 21+ адаптирована для virtual threads. Когда VT нужно ждать на lock, JVM может его unmount и освободить carrier.

**Стратегия 2: Избегай synchronized везде**
```java
// Вместо:
// public synchronized void method() { }

// Используй:
private final Object lock = new Object();
public void method() {
    ReentrantLock lock = new ReentrantLock();
    lock.lock();
    try { ... } finally { lock.unlock(); }
}
```

**Стратегия 3: Кешируй результаты native вызовов**
```java
// ❌ Плохо:
void loop() {
    for (int i = 0; i < 1000; i++) {
        expensiveNativeCall();  // каждый раз pinning
    }
}

// ✅ Хорошо:
void loop() {
    Object cachedResult = expensiveNativeCall();  // один раз
    for (int i = 0; i < 1000; i++) {
        useResult(cachedResult);
    }
}
```

### 4.4 Диагностика Pinning

#### Java Flight Recorder (JFR)
```bash
jcmd <PID> JFR.start duration=60s filename=vt-pinning.jfr \
  settings=profile event=jdk.VirtualThreadPinned

# Затем откройте в jdk.jfr.tool или JMC:
jfr print vt-pinning.jfr | grep -i pinned
```

#### Programmatic detection (Java 21+)
```java
VirtualThreadPinnedEvent listener = (event) -> {
    if (event.getDuration().toMillis() > 10) {
        logger.warn("Pinned for {} ms: {}", 
            event.getDuration().toMillis(),
            event.getStackTrace());
    }
};
```

#### Метрики Micrometer
```java
@Scheduled(fixedRate = 5000)
void monitorPinning() {
    long pinned = ManagementFactory
        .getPlatformMXBeans(ThreadMXBean.class)
        .stream()
        .filter(t -> /* custom detection */)
        .count();
    meter.gauge("jvm.threads.pinned", pinned);
}
```

---

## 5. Когда использовать Virtual Threads, когда избегать

### 5.1 ✅ Идеальные случаи

- **REST API с I/O**: Тысячи одновременных request-ов, каждый делает DB-запрос
- **Микросервисы**: Множество HTTP-вызовов между сервисами
- **WebSocket/Server-Sent Events**: Long-lived connections
- **Batch I/O обработка**: Параллельное чтение миллионов файлов
- **Connection pooling**: Заменяет async frameworks для стандартного J2EE

### 5.2 ❌ Случаи, когда избегать или осторожно

- **CPU-bound compute**: Virtual threads не дают выгоды для вычислений. Используй ExecutorService с платформенными потоками.
- **Синхронизованные коллекции**: `Collections.synchronizedMap()` pinning. Переходи на ConcurrentHashMap.
- **BlockingQueue с синхронизацией**: Может быть ОК, но лучше non-blocking очереди.
- **Legacy code с обилием synchronized**: Требует рефакторинга, высокий риск performance regression.

### 5.3 Гибридный подход

```java
// CPU-bound задачи на platform threads (ForkJoinPool):
ExecutorService cpuPool = Executors.newFixedThreadPool(
    Runtime.getRuntime().availableProcessors()
);

// I/O-bound на virtual threads:
ExecutorService ioPool = Executors.newVirtualThreadPerTaskExecutor();

// Комбинирование:
cpuPool.submit(() -> {
    var data = expensiveCompute();
    ioPool.submit(() -> writeToDatabase(data));
});
```

---

## 6. Production Pitfalls: Spring/Spring Boot, Tomcat, JDBC, JPA/Hibernate

### 6.1 Spring Boot 3.2+ Virtual Thread Support

**Что работает out-of-the-box**:
```yaml
spring:
  threads:
    virtual:
      enabled: true  # WebFlux и Servlet DispatcherServlet используют VT
```

**Томат (Tomcat 10.1+)**:
```java
@Configuration
class TomcatConfig {
    @Bean
    public ConfigurableWebServerFactory webServerFactory() {
        TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
        factory.addConnectorCustomizers(connector -> {
            connector.setProperty("useVirtualThreads", "true");
            connector.setProtocol("org.apache.coyote.http11.Http11NioProtocol");
        });
        return factory;
    }
}
```

### 6.2 JDBC и Connection Pools (HikariCP, Tomcat Pool)

**Проблема**: Если connection pool использует синхронизированные коллекции (старые версии), может быть pinning при получении соединения.

**Решение**:
- Обновись до HikariCP 5.0+ (добавлена поддержка VT)
- Используй `HikariConfig.setThreadFactory(Thread.ofVirtual().factory())`

```java
@Configuration
class DataSourceConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:postgresql://...");
        config.setMaximumPoolSize(10);  // может быть меньше с VT
        config.setThreadFactory(Thread.ofVirtual().factory());
        return new HikariDataSource(config);
    }
}
```

### 6.3 JPA/Hibernate

**Статус Java 21**: Hibernate 6.3+ добавил поддержку virtual threads, но требует тестирования.

**Потенциальные проблемы**:
- **Lazy loading** в синхронном контексте может pinned'ить
- **Session/Transaction management** с `@Transactional` — обычно OK

```java
@Service
public class UserService {
    @Transactional  // Spring управляет transaction на VT
    public List<User> findAll() {
        return userRepository.findAll();  // JDBC операции
    }
}
```

### 6.4 ThreadLocal: от JEP 429 к Scoped Values

**Проблема ThreadLocal**: Virtual threads создаются часто, ThreadLocal хранит данные per-thread, что приводит к memory leak'ам.

**Java 21: Scoped Values (preview, Java 21)**:
```java
final static ScopedValue<String> REQUEST_ID = ScopedValue.newInstance();

void handleRequest(String id) {
    ScopedValue.where(REQUEST_ID, id).run(() -> {
        doWork();  // REQUEST_ID.get() == id
    });
}
```

**Преимущества**:
- Автоматическая очистка при выходе из scope
- Нет memory leak'ов
- Inheritable for child VT (с Java 22+ улучшена семантика)

### 6.5 Locks и RWLock

**Проблема**: `ReentrantReadWriteLock` может быть heavy для VT-интенсивных сценариев.

**Решение**: Рассмотри stamped locks или optimistic reads (если applicable):
```java
private final StampedLock lock = new StampedLock();

public String readValue() {
    long stamp = lock.tryOptimisticRead();
    String result = value;
    if (!lock.validate(stamp)) {
        // Retry with proper locking
        stamp = lock.readLock();
        try {
            result = value;
        } finally {
            lock.unlockRead(stamp);
        }
    }
    return result;
}
```

### 6.6 Observability и Monitoring

**Метрики для отслеживания**:
```java
@Component
class VirtualThreadMetrics {
    private final MeterRegistry registry;
    
    @PostConstruct
    void setup() {
        // Из ThreadMXBean:
        Gauge.builder("jvm.threads.virtual.count",
                () -> ManagementFactory.getThreadMXBean()
                    .getThreadCount(), // только VT
                "threads")
            .register(registry);
        
        // Custom metrics:
        Timer blockedOnIO = Timer.builder("jvm.virtual.io.blocked")
            .publishPercentiles(0.5, 0.95, 0.99)
            .register(registry);
    }
}
```

**Traces (OpenTelemetry)**:
```java
// Автоматический context propagation для VT
var tracer = GlobalOpenTelemetry.getTracer("app");
var span = tracer.spanBuilder("virtual-task")
    .setAttribute("thread.virtual", true)
    .startSpan();

try (var scope = span.makeCurrent()) {
    doWork();
}
```

---

## 7. Structured Concurrency и Scoped Values (Java 21+ preview)

### 7.1 Structured Concurrency (JEP 428, preview в Java 21)

**Цель**: Явная иерархия задач, гарантия завершения.

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Subtask<String> task1 = scope.fork(() -> fetchData1());
    Subtask<String> task2 = scope.fork(() -> fetchData2());
    
    scope.join();  // Ждём обеих
    scope.throwIfFailed();
    
    String result = task1.get() + task2.get();
}
// Гарантия: все подзадачи завершены или cancelled
```

**Статус Java 22+**: Second preview, планируется финализация в Java 23.

### 7.2 Scoped Values (JEP 429, preview в Java 21, Java 22+)

Замена ThreadLocal для immutable per-scope данных:

```java
final static ScopedValue<User> CURRENT_USER = ScopedValue.newInstance();

void service(User user) {
    ScopedValue.where(CURRENT_USER, user)
        .run(() -> processRequest());
    // user more accessible to all called methods
}

void processRequest() {
    var user = CURRENT_USER.get();  // OK
}
```

**Преимущества**:
- Нет memory leak при virtual threads
- Inheritable for spawned VT (задекларировано в JEP)
- Type-safe, compile-time checking

**Статус**: Java 21 preview, Java 22 second preview, планируется finalization в Java 23.

---

## 8. Миграция, тестирование, метрики

### 8.1 Миграция план

#### Фаза 1: Audit
```bash
# Найди все synchronized:
grep -r "synchronized" src/

# Найди все ThreadLocal:
grep -r "ThreadLocal" src/

# Найди все использования старых executor'ов:
grep -r "newFixedThreadPool\|newCachedThreadPool" src/
```

#### Фаза 2: Рефакторинг (по приоритету)
1. Замени `synchronized` на `ReentrantLock` в hot paths
2. Замени `ThreadLocal` на `ScopedValue` (preview, но стабильно)
3. Обнови connection pools (HikariCP 5.0+)
4. Обнови ORM (Hibernate 6.3+)

#### Фаза 3: Включение (поэтапно)
```yaml
# Dev/Test:
spring.threads.virtual.enabled: true

# Staging (1-2 недели):
spring.threads.virtual.enabled: true
# + мониторинг pinning, метрики

# Production (постепенно через canary/blue-green)
```

### 8.2 Тестирование

**Unit тесты** — работают как обычно, но можешь форсировать VT:
```java
@Test
void testWithVirtualThread() throws Exception {
    Thread vt = Thread.ofVirtual()
        .start(() -> {
            // тестовый код
        });
    vt.join();
}
```

**Integration/Load тесты**:
```bash
# JMeter с VT:
# Используй Thread Group → Virtual Threads (JMeter 5.5+)

# Locust:
locust -f loadtest.py --users 10000 --hatch-rate 1000
# Virtual threads делают это реальным без OOM
```

**Stress-тест pinning**:
```java
@Test
void stressPinning() {
    List<Thread> threads = new ArrayList<>();
    for (int i = 0; i < 1000; i++) {
        threads.add(Thread.ofVirtual().start(() -> {
            synchronized (this) {  // ❌ Намеренно pinning
                Thread.sleep(10);
            }
        }));
    }
    threads.forEach(t -> {
        try { t.join(); } catch (InterruptedException e) {}
    });
    // Measure time: должен быть медленнее, чем без synchronized
}
```

### 8.3 Метрики и SLI/SLO

**Ключевые метрики**:

| Метрика | Как измерить | SLI/Target |
|---------|-------------|-----------|
| VT throughput | request/sec на серверу | +30–50% vs platform |
| P99 latency | 99-й перцентиль response time | < 200 ms |
| Pinned duration | JFR event avg duration | < 5 ms (warning > 10 ms) |
| Memory footprint | RSS, heap size | -20–30% vs platform |
| GC pause time | G1GC, ZGC logs | < 10 ms (99%) |

**Prometheus metrics**:
```yaml
jvm_threads_virtual_count{application="api"} 50000
jvm_threads_platform_count{application="api"} 16
jvm_virtual_pinned_duration_ms{bucket="100ms"} 42
```

---

## 9. Пять–восемь практических takeaway'ев для senior engineer

### 1. **Virtual Threads = масштабируемость I/O, НЕ производительность вычисли**
   - ✅ Используй для REST API, микросервисов, DB queries
   - ❌ НЕ используй для CPU-bound tasks (matrix calculations, crypto)

### 2. **Pinning = молчаливый killer производительности**
   - Одна строка `synchronized` в hot path полностью нейтрализует выгоду VT
   - **Обязательно** профилируй JFR с `jdk.VirtualThreadPinned` на staging перед production
   - Замени все `synchronized` на `ReentrantLock` proactively

### 3. **ThreadLocal → Scoped Values (Java 21+)**
   - `ThreadLocal.withInitial()` приведёт к memory leak при 1M VT
   - `ScopedValue` хоть ещё в preview, но **это будущее** и уже стабильно
   - Миграция обычно безболезненна: `get()` вместо `get()`, но инвертирование управления scope'ом

### 4. **Ecosystem support в Java 21 LTS неполный, улучшается**
   - Spring Boot 3.2+ ✅
   - Hibernate 6.3+ ✅ (требует тестирования)
   - HikariCP 5.0+ ✅
   - Netty, Vert.x пока не оптимизированы (используют custom schedulers)
   - **Шаг 0**: Обновить все зависимости

### 5. **Carrier thread pool по умолчанию small (примерно на ядро)**
   - `-Djdk.virtualThreadScheduler.parallelism=N` для override
   - На high-concurrency сценариях может быть узким местом
   - Мониторь `jdk.VirtualThreadScheduler*` метрики в JMX

### 6. **Connection pools и async pools могут быть контрпродуктивны**
   - Старый паттерн: 200-потовый pool для 200 одновременных request'ов
   - Новый паттерн: 10K VT + маленький connection pool (10–20)
   - **Ловушка**: Забыл изменить pool size на production → connection starvation

### 7. **Foreign Function & Memory API (java.lang.foreign) требует care**
   - Confined arena = pinning гарантирован
   - Shared arena = может быть OK, но test needed
   - Используй native code call wrapper'ы с explicit thread-affinity, где возможно

### 8. **Structured Concurrency (Java 22+ second preview) меняет exception handling**
   - Явная иерархия задач вместо `CountDownLatch` / `ExecutorService`
   - `ExceptionGroup` вместо single exception для multiple failures
   - Готово к finalization, можешь использовать даже в Java 21 LTS с preview flags

---

## 10. Проверяемые источники и версии

### Официальная документация (JEP, Oracle)
- **JEP 444: Virtual Threads** (финализирована в Java 21 LTS)  
  https://openjdk.org/jeps/444
  
- **JEP 428: Structured Concurrency** (preview Java 21, second preview Java 22)  
  https://openjdk.org/jeps/428
  
- **JEP 429: Scoped Values** (preview Java 21, second preview Java 22)  
  https://openjdk.org/jeps/429
  
- **JEP 439: Generational ZGC** (recommends для VT + large heap)  
  https://openjdk.org/jeps/439

### Release Notes
- **Java 21 LTS (Sept 2023)**:  
  https://jdk.java.net/21/release-notes
  
- **Java 22 (March 2024)**: https://jdk.java.net/22/release-notes

### Качественные источники (blogs, articles)
- **Ron Pressler (Virtual Threads lead, Oracle)**:  
  "Virtual threads and structured concurrency" — Devoxx talks (YouTube)
  
- **Gunnar Morling (Red Hat/Quarkus)**:  
  "Virtual Threads – Under the Hood" — blog.gunnarmorling.de
  
- **Helidon Project (Oracle microservices)**:  
  "Helidon and Virtual Threads" guide — https://helidon.io/docs/
  
- **Spring Blog**:  
  "Virtual Threads in Spring Boot 3.2" (Dec 2023)  
  https://spring.io/blog/

- **Micrometer + Spring Cloud Observability docs** (для metrics):  
  https://micrometer.io/docs/guide/registry-micrometer-observation-handler

### Репозитории исходных кодов
- **JDK 21 virtual threads implementation**:  
  https://github.com/openjdk/jdk (search: `VirtualThread`, `ForkJoinPool`)
  
- **HikariCP 5.0+ virtual thread support**:  
  https://github.com/brettwooldridge/HikariCP/releases/tag/5.0.0
  
- **Hibernate 6.3+ virtual thread support**:  
  https://hibernate.org/orm/releases/6.3/ (changelog)

### Инструменты диагностики
- **Java Flight Recorder Events**:  
  `jdk.VirtualThreadPinned`, `jdk.VirtualThreadMount`, `jdk.VirtualThreadUnmount`  
  Docs: https://docs.oracle.com/en/java/javase/21/jfr/reference.html
  
- **jcmd для интроспекции**:
  ```bash
  jcmd <pid> VM.info  # Info о virtual threads
  jcmd <pid> JFR.start event=jdk.VirtualThreadPinned
  ```

---

## 11. Заключение

Virtual Threads в Java 21 LTS — это **production-ready инвестиция** для I/O-bound приложений, которая не требует переписывания на async. Выигрыш может быть 30–50% throughput улучшение на REST API с minimal кода изменения.

Однако, это требует:
1. **Глубокого понимания pinning** и его диагностики
2. **Вдумчивой миграции** (synchronized → ReentrantLock, ThreadLocal → ScopedValue)
3. **Постоянного мониторинга** на production (pinning metrics, GC, memory)

**Recommendation для production Java 21 LTS**:
- ✅ Используй на новых greenfield проектах сразу
- ⚠️ Мигрируй legacy поэтапно (audit → refactor → canary → full)
- 🚫 НЕ полагайся на `spring.threads.virtual.enabled=true` как на серебряную пулю

Версионирование: документ актуален для Java 21 LTS (Sept 2023), Java 22–23 (preview features, информация собрана Sept 2024).