# Multithreading & Concurrency 🧵

Параллельное выполнение задач для ускорения работы программ.

## 1. Основы потоков
- `Thread` vs `Runnable`: Класс vs Интерфейс.
- `start()` vs `run()`: `start()` создает новый поток, `run()` просто выполняет код в текущем.

## 2. Синхронизация и Ключевые слова
- **`synchronized`** — блокировка монитора объекта/класса. Только один поток в один момент времени.
- **`volatile`** — гарантирует чтение значения из основной памяти (Main Memory), а не из кэша процессора. Решает проблему видимости.
- **`wait()` / `notify()`** — механизм взаимодействия потоков.

## 3. j.u.c (java.util.concurrent)
- **ExecutorService** — пул потоков (`FixedThreadPool`, `CachedThreadPool`).
- **Locks** (`ReentrantLock`) — более гибкая замена `synchronized`.
- **Atomics** (`AtomicInteger`) — неблокирующие атомарные операции (CAS - Compare And Swap).
- **Синхронизаторы**: `CountDownLatch`, `CyclicBarrier`, `Semaphore`.

## 4. Virtual Threads (Java 21+)
Революция в Java. Позволяют создавать миллионы потоков без нагрузки на ОС, так как они управляются самой JVM (Project Loom). Идеально для I/O задач.

---

[⬅️ Назад к Java Index](./README.md)
