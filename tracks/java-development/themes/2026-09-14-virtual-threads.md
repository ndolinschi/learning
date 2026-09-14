# Virtual Threads в Java 21+: модель, pinning, когда использовать, типичные ошибки

- **Трек:** Java Development
- **Дата:** 2026-09-14
- **Статус:** ✅ изучено (ежедневный drop)
- **Notebook:** https://notebook.google.com/notebook/4048c827-99c4-4a38-9939-2f825bfc4bf4
- **Claude chat:** https://claude.ai/chat/c96dee96-18af-4cf8-9f8b-f504cc6143f3

## Кратко

Виртуальные потоки (JEP 444) — лёгкие потоки JVM для I/O-bound нагрузки. Код остаётся синхронным; JVM мультиплексирует миллионы VT на небольшой пул carrier threads. Главный риск — **pinning** carrier thread (`synchronized`, native/JNI), который убивает масштабирование.

## Ключевые идеи

1. VT ≠ замена platform threads для CPU-bound работы.
2. Mount/unmount на blocking I/O; pinning удерживает carrier.
3. Практический API: `Thread.ofVirtual()`, `Executors.newVirtualThreadPerTaskExecutor()`.
4. Осторожно с `ThreadLocal`, пулами под VT, connection pools (JDBC/JPA).
5. Structured Concurrency и Scoped Values — соседние темы.

## Материалы

- Презентация и аудио/видео: в notebook выше
- Research / doc: локальный drop `learning-2026-09-14` на компьютере бота

## Источники

JEP 444, 428, 429; OpenJDK release notes; Spring/Helidon/Hikari notes — см. research brief.
