# TypeScript: Типы и Интерфейсы 🔷

В TypeScript есть два основных способа описать форму объекта: `type` и `interface`. Они похожи, но имеют отличия.

## 1. Interface (Интерфейс)
Интерфейсы лучше всего подходят для описания **объектов** и **классов**. Они поддерживают расширение через `extends` и "слияние" (declaration merging).

```typescript
// Описание структуры пользователя
interface User {
  id: number;
  name: string;
  email: string;
  role?: 'admin' | 'user'; // Опциональное поле
}

// Расширение интерфейса
interface Admin extends User {
  permissions: string[];
}

const admin: Admin = {
  id: 1,
  name: "Ivan",
  email: "ivan@test.com",
  permissions: ["read", "write"]
};
```

## 2. Type Alias (Псевдоним типа)
Типы более гибкие. Они позволяют описывать **Union** (объединения), **Tuples** (кортежи) и **Primititve** алиасы.

```typescript
// Union Type (Один из вариантов)
type Status = 'pending' | 'success' | 'error';

// Кортеж (Tuple)
type Point = [number, number];

// Пересечение типов (Intersection)
type ContactInfo = {
  phone: string;
  address: string;
};

type Profile = User & ContactInfo; // Содержит все поля из User и ContactInfo
```

## 3. Generics (Обобщения)
Позволяют создавать переиспользуемые компоненты, работающие с разными типами.

```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// Пример использования с типом User
const userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Alice", email: "alice@test.com" },
  status: 200,
  message: "Success"
};
```

## 4. Utility Types
Полезные встроенные инструменты:
- `Partial<T>` — делает все поля объекта необязательными.
- `Pick<T, 'id' | 'name'>` — выбирает только указанные поля.
- `Omit<T, 'email'>` — исключает указанные поля.

---

[⬅️ Назад к TypeScript Index](./README.md)
