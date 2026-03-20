# React Hooks: Подробный разбор ⚛️

Хуки — это функции, которые позволяют "подцепиться" к состоянию и методам жизненного цикла React из функциональных компонентов.

## 1. useState: Управление состоянием
Самый важный хук. Позволяет компоненту хранить данные между рендерами.

```javascript
import React, { useState } from 'react';

function Counter() {
  // count - текущее значение, setCount - функция обновления
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Вы нажали {count} раз</p>
      <button onClick={() => setCount(count + 1)}>
        Нажми меня
      </button>
    </div>
  );
}
```

## 2. useEffect: Сайд-эффекты
Используется для запросов к API, подписок или манипуляций с DOM. Заменяет методы `componentDidMount`, `componentDidUpdate` и `componentWillUnmount`.

```javascript
useEffect(() => {
  // Код выполнится после рендера
  const timer = setInterval(() => console.log('Tick'), 1000);

  // Функция очистки (cleanup) - выполнится перед удалением компонента
  return () => clearInterval(timer);
}, []); // [] - пустой массив зависимостей: выполнится 1 раз при монтировании
```

## 3. useContext: Глобальные данные
Позволяет избежать "Prop Drilling" (прокидывания пропсов через много уровней).

```javascript
const ThemeContext = React.createContext('light');

function Display() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>Текущая тема: {theme}</div>;
}
```

## 4. useMemo и useCallback: Оптимизация
- **useMemo**: Кэширует **результат** вычислений.
- **useCallback**: Кэширует саму **функцию**, чтобы не создавать её заново при каждом рендере.

```javascript
// useMemo: пересчитает только если изменится data
const expensiveValue = useMemo(() => compute(data), [data]);

// useCallback: ссылка на функцию останется стабильной
const handleClick = useCallback(() => {
  console.log('Clicked!');
}, []);
```

## 5. useRef: Ссылки на DOM и хранение данных
Позволяет получить доступ к DOM-узлу напрямую или хранить значение, изменение которого **не вызывает** повторный рендер.

```javascript
const inputRef = useRef(null);

const focusInput = () => {
  inputRef.current.focus(); // Прямой доступ к input
};
```

---

[⬅️ Назад к Framework Index](./README.md)
