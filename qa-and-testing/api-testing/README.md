# API Testing 🔌

Тестирование бэкенда через HTTP-запросы без использования интерфейса.

## 1. Инструменты
- **Postman** — GUI инструмент для ручных запросов и коллекций.
- **RestAssured** — Библиотека Java для автоматизации API-тестов.

## 2. RestAssured Flow (Given-When-Then)
```java
given()
    .contentType(JSON)
    .body(requestDto)
when()
    .post("/api/users")
then()
    .statusCode(201)
    .body("username", equalTo("dmitri"));
```

## 3. Что проверять в API
- Статус-коды (200, 201, 400, 401, 404, 500).
- Структура JSON (схема).
- Время ответа.
- Заголовки (Headers, Cookies).

---

[⬅️ Назад к QA Index](../README.md)