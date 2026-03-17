# Generics & Reflection 🔍

Продвинутые механизмы работы с типами и метаданными.

## 1. Generics (Обобщения)
Позволяют писать параметризованный код.
- **Type Erasure** — во время выполнения информация о типах стирается (для обратной совместимости).
- **Wildcards**:
  - `? extends T` — (Upper Bound) Чтение.
  - `? super T` — (Lower Bound) Запись.
  - *PECS rule*: **P**roducer **E**xtends, **C**onsumer **S**uper.

## 2. Reflection API
Возможность программы изучать и изменять свою структуру во время выполнения (классы, методы, поля).
- Используется в Spring для Dependency Injection и Hibernate для маппинга.
- Позволяет получить доступ к `private` полям.

## 3. Annotations (Аннотации)
Метаданные, прикрепляемые к коду.
- `@Retention(RetentionPolicy.RUNTIME)` — доступна во время выполнения через рефлексию.
- `@Target` — где можно использовать (метод, поле, класс).

---

[⬅️ Назад к Java Index](./README.md)
