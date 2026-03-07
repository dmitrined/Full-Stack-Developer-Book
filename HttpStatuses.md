# HTTP Status Codes (Коды ответов) 🌐

В REST API коды состояния (status codes) — это стандартный способ сообщить клиенту о результате его запроса. В Spring Boot мы управляем ими через перечисление `HttpStatus` и объект `ResponseEntity`.

---

## 🟢 2xx: Success (Успешно)

Эти коды означают, что запрос был успешно принят и обработан.

| Код | Имя в Spring | Описание | Когда использовать |
| :--- | :--- | :--- | :--- |
| **200** | `OK` | Стандартный успех. | Обычные `GET` запросы или успешные `PUT`/`PATCH`. |
| **201** | `CREATED` | Ресурс успешно создан. | После успешного `POST` запроса (регистрация, новый пост). |
| **204** | `NO_CONTENT` | Успех, но тела ответа нет. | После успешного `DELETE` (запись удалена, возвращать нечего). |

---

## 🟡 4xx: Client Error (Ошибка клиента)

Эти коды говорят о том, что проблема на стороне того, кто делал запрос (неверные данные, нет прав).

| Код | Имя в Spring | Описание | Когда использовать |
| :--- | :--- | :--- | :--- |
| **400** | `BAD_REQUEST` | Неверный запрос. | Ошибки валидации (пустое поле, неверный формат email). |
| **401** | `UNAUTHORIZED` | Не авторизован. | Пользователь не залогинился (нет токена). |
| **403** | `FORBIDDEN` | Доступ запрещен. | Пользователь залогинился, но у него **недостаточно ролей** (например, юзер лезет в админку). |
| **404** | `NOT_FOUND` | Не найдено. | Ссылка не существует или ресурс с таким ID не найден в БД. |
| **409** | `CONFLICT` | Конфликт. | Попытка создать дубликат пользователя с тем же email. |

---

## 🔴 5xx: Server Error (Ошибка сервера)

Проблема на стороне вашего кода или инфраструктуры.

| Код | Имя в Spring | Описание | Когда использовать |
| :--- | :--- | :--- | :--- |
| **500** | `INTERNAL_SERVER_ERROR` | Внутренняя ошибка сервера. | Необработанный `NullPointerException`, падение БД, баг в логике. |
| **503** | `SERVICE_UNAVAILABLE` | Сервис недоступен. | Сервер перегружен или на тех. обслуживании. |

---

## 💻 Использование в Spring Boot

### 1. В Контроллере (`ResponseEntity`)
Самый гибкий способ. Позволяет задать и статус, и тело ответа.

```java
@PostMapping
public ResponseEntity<UserResponse> create(@RequestBody UserRequest request) {
    UserResponse created = userService.save(request);
    // Возвращаем 201 Created
    return ResponseEntity.status(HttpStatus.CREATED).body(created);
}

@DeleteMapping("/{id}")
public ResponseEntity<Void> delete(@PathVariable Long id) {
    userService.delete(id);
    // Возвращаем 204 No Content
    return ResponseEntity.noContent().build();
}
```

### 2. Через аннотацию `@ResponseStatus`
Удобно для простых методов, которые всегда возвращают один и тот же статус.

```java
@PostMapping("/quick-log")
@ResponseStatus(HttpStatus.ACCEPTED) // Возвращает 202 Accepted
public void logAction(@RequestBody String message) {
    log.info(message);
}
```

### 3. В Глобальном Обработчике (`@ControllerAdvice`)
Централизованное управление ответами на ошибки.

```java
@ExceptionHandler(ResourceNotFoundException.class)
public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
    // Автоматическая отправка 404 при выбрасывании исключения
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
}
```

---

## 💡 Полезные советы:
1. **Не используйте 200 для ошибок**: Никогда не возвращайте `200 OK` с телом `{"error": "not found"}`. Это сбивает с толку кеши и инструменты мониторинга.
2. **401 vs 403**: 401 — "Я не знаю, кто ты" (нужен логин). 403 — "Я знаю, кто ты, но тебе сюда нельзя" (нужна роль).
3. **500 — это плохо**: Если ваше API часто отдает 500, значит, в коде есть дыры, которые нужно закрыть через `@ExceptionHandler`.
