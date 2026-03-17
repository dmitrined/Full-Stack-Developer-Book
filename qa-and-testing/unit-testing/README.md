# Unit Testing (JUnit & Mockito) 🧩

Проверка мельчайших частей приложения в изоляции.

## 1. JUnit 5 (Jupiter)
Главный фреймворк для тестов в Java.
- `@Test` — помечает метод как тест.
- `@BeforeEach` / `@AfterEach` — запуск перед/после каждого теста.
- `@BeforeAll` / `@AfterAll` — запуск один раз для класса ( static методы).
- `Assertions.assertEquals(expected, actual)` — проверка результата.

## 2. Mockito
Библиотека для создания "заглушек" (Mocks) зависимостей.
- `@Mock` — создает пустышку.
- `@InjectMocks` — вставляет моки в тестируемый класс.
- `when(dao.find(1)).thenReturn(user)` — настройка поведения.
- `verify(service, times(1)).save()` — проверка вызова метода.

## 3. Best Practices (F.I.R.S.T)
- **F**ast (Быстрые).
- **I**ndependent (Независимые).
- **R**epeatable (Повторяемые).
- **S**elf-validating (Самопроверяемые).
- **T**horough (Тщательные).

---

[⬅️ Назад к QA Index](../README.md)
