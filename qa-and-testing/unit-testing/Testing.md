# Тестирование (Testing)

Тесты — это гарантия того, что ваш код работает правильно и не сломается завтра при добавлении новых функций.

---

### Зачем вообще нужно тестирование?
Частый вопрос: *«Зачем писать тесты, если код и так работает?»*
Проблема в том, что “работает” — это субъективное ощущение разработчика на пару сценариев. Тесты — это объективная проверка поведения системы с учетом граничных случаев и неправильного ввода. Ошибка, которая на первый взгляд не видна, может стоить компании очень дорого.

**Цена ошибки**:
- **В процессе написания кода** — исправить дешево.
- **Во время интеграции** — дороже.
- **В продакшене** — максимально дорого (страдает репутация, срочные хотфиксы, недовольство пользователей). 
Тестирование минимизирует риск пропустить ошибку в продакшен.

---

### Пирамида тестирования
Идеальное соотношение тестов в приложении:

1. **Unit-тесты (Модульные)** — база пирамиды. Самые быстрые, изолированные и дешёвые. Тестируют один класс или метод (например, переключение статуса задачи). Их должно быть больше всего.
2. **Integration-тесты (Интеграционные)** — середина. Поднимают контекст Spring, тестируют взаимодействие баз данных и сервисов. Их меньше, они медленнее.
3. **UI / E2E тесты** — вершина. Имитируют работу реального пользователя в браузере (нажать кнопку формы логина). Самые медленные и хрупкие. Пишутся только для самых критических сценариев.

---

### Баланс
Нельзя писать только UI-тесты или обходиться без тестов вообще. Плохо тестируемый код обычно сильно связан и имеет плохую архитектуру (нарушается Dependency Injection). Тестируемый код всегда имеет четкие слои и легко поддерживается.

---

### 1. Типы тестов в Spring Boot

#### Unit-тесты (Модульные)
- Тестируют **один маленький кусочек** кода (обычно один метод в сервисе).
- Работают очень быстро.
- Не запускают всё приложение и не подключаются к базе данных.
- Все зависимости "подменяются" заглушками (Mocks).

#### Integration-tests (Интеграционные)
- Тестируют **взаимодействие** разных частей приложения.
- Например: Контроллер -> Сервис -> Репозиторий -> База данных.
- Работают медленнее, так как запускают контекст Spring.

---

### 2. Главные аннотации

| Аннотация | Описание |
| :--- | :--- |
| `@Test` | Помечает метод как тестовый. |
| `@SpringBootTest` | Запускает полный контекст приложения для интеграционных тестов. |
| `@MockBean` / `@Mock` | Создает "пустышку" вместо реального объекта. Вы можете диктовать ей, что возвращать. |
| `@InjectMocks` | Создает реальный объект и вставляет в него созданные `@Mock`. |
| `@MockMvc` | Утилита для имитации HTTP-запросов к вашим контроллерам без запуска сервера. |
| `@Spy` | Позволяет следить за реальным объектом, подменяя только часть его методов. |
| `@ExtendWith` | Расширяет поведение тестов (например, с `MockitoExtension.class` для активации моков). |
| `@AutoConfigureMockMvc` | Автоматически настраивает `MockMvc` для интеграционных тестов. |
| `@Autowired` | Внедряет зависимость (bean) из контекста Spring прямо в тестовый класс. |

---

### 3. Пример Unit-теста (Mockito)
Здесь мы тестируем `UserService` в полной изоляции от базы данных.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository; // Создаем мок (заглушку) репозитория

    @InjectMocks
    private UserService userService; // Внедряем мок в тестируемый сервис

    @Test
    void shouldCreateUser_WhenDataIsValid() {
        // 1. Arrange (Подготовка)
        UserDTO dto = new UserDTO("ivan@test.com", "pass123");
        User savedUser = new User(1L, "ivan@test.com", "encoded_pass");
        
        // Обучаем мок: при вызове save() вернуть объект с ID
        when(userRepository.save(any(User.class))).thenReturn(savedUser);

        // 2. Act (Действие)
        UserResponse result = userService.createUser(dto);

        // 3. Assert (Проверка)
        assertNotNull(result.getId());
        assertEquals("ivan@test.com", result.getEmail());
        
        // Проверяем, что метод save вызывался ровно 1 раз
        verify(userRepository, times(1)).save(any(User.class));
    }

    @Test
    void shouldThrowException_WhenUserAlreadyExists() {
        when(userRepository.existsByEmail("exist@test.com")).thenReturn(true);

        // Проверяем, что сервис выбрасывает ожидаемое исключение
        assertThrows(UserAlreadyExistsException.class, () -> {
            userService.createUser(new UserDTO("exist@test.com", "pass"));
        });
    }
}
```

---

### 4. Полезные методы Mockito
- `when(...).thenReturn(...)` — задать возвращаемое значение.
- `when(...).thenThrow(...)` — имитировать выброс исключения.
- `verify(mock, times(n)).method()` — проверить количество вызовов.
- `any()`, `anyString()`, `anyLong()` — аргумент-матчеры для гибкости.
- `doNothing().when(mock).voidMethod()` — для методов, которые ничего не возвращают.

---

### 5. Integration Tests (Интеграционные тесты)
Тестируем, как разные слои работают вместе. Здесь поднимается реальный Spring Context.

```java
@SpringBootTest 
@AutoConfigureMockMvc // Позволяет отправлять фейковые HTTP запросы
class UserControllerIT {

    @Autowired
    private MockMvc mockMvc; 

    @Autowired
    private ObjectMapper objectMapper; // Для превращения объектов в JSON (и обратно)

    @MockBean
    private UserService userService; // Замокаем сервис

    @Test
    void shouldReturn201OnSuccessfulRegistration() throws Exception {
        RegisterRequest request = new RegisterRequest("Ivan", "ivan@gmail.com", "password");
        
        when(userService.register(any(RegisterRequest.class))).thenReturn(null);

        mockMvc.perform(post("/api/users/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isCreated()); // Ожидаем ответ 201 Created
    }
}
```

---

### 3. UI-тесты / E2E (End-to-End)
Тестируем поведение реального пользователя через браузер. Обычно используются инструменты типа **Selenium** или **Selenide**. Поднимаем сервер на реальном порту и пишем код, который "кликает" по кнопкам.

```java
package com.example.ui;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertEquals;

// Тест поднимет приложение на порту 8080 для браузера
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
class UserRegistrationE2ETest {

    private WebDriver driver;

    @BeforeEach
    void setUp() {
        // Инициализируем Chrome драйвер
        driver = new ChromeDriver();
    }

    @Test
    void userShouldBeAbleToRegister() {
        // 1. Открываем страницу регистрации (например, созданную через Thymeleaf)
        driver.get("http://localhost:8080/register");

        // 2. Находим элементы на странице по их ID
        WebElement nameInput = driver.findElement(By.id("username"));
        WebElement emailInput = driver.findElement(By.id("email"));
        WebElement passwordInput = driver.findElement(By.id("password"));
        WebElement submitButton = driver.findElement(By.id("submit-btn"));

        // 3. Имитируем ввод текста пользователем
        nameInput.sendKeys("Ivan");
        emailInput.sendKeys("ivan@gmail.com");
        passwordInput.sendKeys("secure123");
        
        // 4. Кликаем кнопку
        submitButton.click();

        // 5. Проверяем, что сервер успешно обработал данные и перекинул на страницу логина
        assertEquals("http://localhost:8080/login", driver.getCurrentUrl());
    }

    @AfterEach
    void tearDown() {
        // Закрываем браузер после каждого теста
        if (driver != null) {
            driver.quit();
        }
    }
}
```
---

### Best Practices
1. **Given-When-Then**: Пишите тесты по этой структуре: Дано (создаем данные) -> Когда (вызываем метод) -> Тогда (проверяем результат).
2. **Изоляция**: Тесты не должны зависеть друг от друга. Один тест упал — остальные должны пройти.
3. **Названия**: Называйте тесты так, чтобы было понятно, что именно сломалось: `saveUser_shouldSaveToDatabase()`.
