
## 🔹 Что такое серверные действия (Server Actions)?

**Server Actions** — это механизм в Next.js, позволяющий обрабатывать формы, отправлять данные на сервер и выполнять серверную логику **без необходимости создавать отдельные API-роуты**. Они выполняются на сервере, но вызываются напрямую из компонентов.

___

## 🔹 Пример проекта

В примере два компонента:

1.  **`Categories`** – асинхронный компонент, загружающий категории с внешнего API.
2.  **`CreateCategory`** – форма, использующая `action={createCategory}`, что указывает на серверное действие.

___

## 🔹 Основы Server Actions

### 📌 1. Где пишутся Server Actions?

Server Actions — это обычные функции, которые помечаются как **`async`** и размещаются в **серверных файлах** (`/app/actions/`, или `server-only.ts`, `action.ts` и т.п.).

```
// Пример server action (файл: /app/actions/createCategory.ts)
'use server';

export async function createCategory(formData: FormData) {
  const name = formData.get("name");
  const image = formData.get("image");

  await fetch("https://api.escuelajs.co/api/v1/categories", {
    method: "POST",
    body: JSON.stringify({ name, image }),
    headers: {
      "Content-Type": "application/json",
    },
  });
}
```

-   Директива `'use server'` указывает Next.js, что эта функция должна выполняться на сервере.
-   Форма передаёт данные через **native HTML form**, без необходимости использовать `useState`, `onSubmit`, и т.п.

___

### 📌 2. Как вызываются Server Actions?

В компоненте форма выглядит так:

```
<form action={createCategory}>
  <input type="text" name="name" placeholder="Name" />
  <input type="text" name="image" placeholder="Image" />
  <button type="submit">Save</button>
</form>
```

-   `action={createCategory}` — означает, что при отправке формы Next.js передаст `FormData` в функцию `createCategory`, и выполнит её **на сервере**.

___

## 🔹 Особенности Server Actions

| Особенность | Описание |
| --- | --- |
| 🔐 Безопасность | Код выполняется на сервере, не попадает в браузер |
| 🧾 FormData | Форма передаёт данные как `FormData`, а не JSON |
| ♻️ Интеграция с кэшированием | Можно использовать `revalidateTag()` для сброса кеша |
| ❌ Без JS на клиенте | Работает даже без JavaScript в браузере |

___

## 🔹 Асинхронные компоненты (например, `Categories`)

```
export default async function Categories() {
  const res = await fetch("https://api.escuelajs.co/api/v1/categories", {
    next: { tags: ["categories"] }, // позволяет использовать revalidateTag
  });

  const categories = await res.json();

  return (
    <ul>
      {categories.map((category: Category) => (
        <li key={category.id}>
          <p>{category.name}</p>
          <Image src={category.image} ... />
        </li>
      ))}
    </ul>
  );
}
```

-   Этот компонент загружает данные **на сервере**, поскольку является `async`.
-   Использование `next: { tags: ["categories"] }` позволяет потом сбросить кеш с помощью `revalidateTag("categories")` в Server Action.

___

## 🔹 Заключение

**Server Actions** в Next.js — это мощный способ обработки форм и логики без написания API endpoints. Они:

-   упрощают архитектуру,
-   работают только на сервере (повышают безопасность),
-   хорошо сочетаются с асинхронными компонентами и кэшированием.
