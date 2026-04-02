
### 💡 Что такое Drizzle ORM?

**Drizzle ORM** — это библиотека для взаимодействия с базами данных (например, PostgreSQL, MySQL, SQLite), написанная на TypeScript. Она позволяет писать SQL-запросы с автодополнением, строгой типизацией и без магии — все запросы явные и контролируемые.

___

### ⚙️ Почему он популярен с Next.js?

Потому что:

-   ✅ Идеально работает с **App Router** и **Edge Functions**.
-   ✅ Полная **типизация на уровне схемы**.
-   ✅ Без runtime-магии — генерируется чистый SQL.
-   ✅ Хорош для **Server Actions** (например, `createPost()` прямо в `action.ts`).

___

### 📦 Пример использования Drizzle в Next.js (PostgreSQL)

#### 1\. Установка

[ссылка](https://orm.drizzle.team/docs/get-started/postgresql-new)

#### 2\. Настройка схемы

```
// db/schema.ts
import { pgTable, serial, text, timestamp } from 'drizzle-orm/pg-core';

export const posts = pgTable('posts', {
  id: serial('id').primaryKey(),
  title: text('title').notNull(),
  createdAt: timestamp('created_at').defaultNow(),
});
```

#### 4\. Использование в Server Action

```
// app/actions.ts
"use server";
import { db } from "@/db";
import { posts } from "@/db/schema";

export async function createPost(title: string) {
  await db.insert(posts).values({ title });
}
```

___

### ✅ Когда использовать Drizzle с Next.js

-   Если ты хочешь **легкий, быстрый и типобезопасный ORM**.
-   Если у тебя **App Router** и **Server Actions**.
-   Если ты предпочитаешь писать SQL в стиле TypeScript без скрытых магий.

Конечно! Вот обзор **основных методов работы в Drizzle ORM** — таких как `insert`, `select`, `update`, `delete`, с краткими примерами. Всё написано на **TypeScript**, так как Drizzle заточен под типобезопасную работу.

___

## 📦 Основные методы Drizzle ORM

> Работает с разными базами (PostgreSQL, MySQL, SQLite). Ниже пример на PostgreSQL, но с другими всё похоже.

___

### ✅ `insert()` — вставка данных

```
await db.insert(users).values({
  name: 'Alice',
  email: 'alice@example.com',
});
```

**Множественная вставка:**

```
await db.insert(users).values([
  { name: 'Alice', email: 'alice@example.com' },
  { name: 'Bob', email: 'bob@example.com' },
]);
```

**С возвратом вставленных данных:**

```
const result = await db.insert(users).values({
  name: 'Charlie',
}).returning(); // вернёт вставленные строки
```

___

### 🔍 `select()` — выборка данных

```
const result = await db.select().from(users);
```

**Фильтрация:**

```
const result = await db
  .select()
  .from(users)
  .where(eq(users.email, 'alice@example.com'));
```

**Выбор отдельных колонок:**

```
const result = await db
  .select({ name: users.name })
  .from(users);
```

**Сортировка:**

```
const result = await db
  .select()
  .from(users)
  .orderBy(desc(users.createdAt));
```

___

### ✏️ `update()` — обновление данных

```
await db
  .update(users)
  .set({ name: 'New name' })
  .where(eq(users.id, 1));
```

**С возвратом:**

```
const updated = await db
  .update(users)
  .set({ email: 'new@example.com' })
  .where(eq(users.id, 2))
  .returning();
```

___

### ❌ `delete()` — удаление

```
await db
  .delete(users)
  .where(eq(users.id, 3));
```

**С возвратом:**

```
const deleted = await db
  .delete(users)
  .where(eq(users.id, 3))
  .returning();
```

___

## 🔗 Условия (`where`) и фильтры

Импортируются из `drizzle-orm`:

```
import { eq, and, or, like, gt, lt } from 'drizzle-orm';
```

**Примеры:**

```
where(eq(users.name, 'Alice'))

where(and(
  eq(users.role, 'admin'),
  gt(users.createdAt, new Date('2024-01-01')),
))
```

___

## 🔀 Join (объединение таблиц)

```
const result = await db
  .select({
    userName: users.name,
    postTitle: posts.title,
  })
  .from(users)
  .innerJoin(posts, eq(users.id, posts.userId));
```

___

## 🧱 Пример полной схемы

```
export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  name: text('name'),
  email: text('email').notNull(),
  createdAt: timestamp('created_at').defaultNow(),
});

export const posts = pgTable('posts', {
  id: serial('id').primaryKey(),
  title: text('title').notNull(),
  userId: integer('user_id').references(() => users.id),
});
```

___

## 🚀 Быстрый итог

| Операция | Метод | Пример |
| --- | --- | --- |
| Вставка | `insert()` | `db.insert(users).values(...)` |
| Чтение | `select()` | `db.select().from(users)` |
| Обновление | `update()` | `db.update(users).set(...).where(...)` |
| Удаление | `delete()` | `db.delete(users).where(...)` |
