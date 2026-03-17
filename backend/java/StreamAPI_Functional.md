# Functional Programming & Stream API ⚡

Переход от императивного к декларативному стилю написания кода (Java 8+).

## 1. Лямбда-выражения
Краткая запись анонимного класса для функционального интерфейса (интерфейс с одним абстрактным методом).
`list.forEach(n -> System.out.println(n));`

## 2. Функциональные интерфейсы
- `Predicate<T>` — принимает T, возвращает `boolean`.
- `Function<T, R>` — принимает T, возвращает R.
- `Consumer<T>` — принимает T, ничего не возвращает.
- `Supplier<T>` — ничего не принимает, возвращает T.

## 3. Stream API
Конвейер обработки данных.
- **Intermediate (Промежуточные)**: `filter`, `map`, `flatmap`, `sorted`, `distinct`, `limit`. (Ленивые, не запускаются без терминальной операции).
- **Terminal (Терминальные)**: `collect`, `forEach`, `count`, `reduce`, `findFirst`, `anyMatch`.

## 4. Optional
Контейнер для значения, которое может быть `null`. Позволяет избежать `NullPointerException`.
- `orElse()`, `ifPresent()`, `map()`, `flatMap()`.

---

[⬅️ Назад к Java Index](./README.md)
