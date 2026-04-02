
> Drizzle ORM — это современная TypeScript ORM (Object-Relational Mapping), ориентированная

---
**Drizzle ORM** — это современная TypeScript ORM (Object-Relational Mapping), ориентированная

## 🧩 Основные типы отношений

1.  **One-to-One (Один к одному)**
2.  **One-to-Many (Один ко многим)**
3.  **Many-to-Many (Многие ко многим)**

___

## 🔧 Как создавать отношения между таблицами

В Drizzle ORM нет автоматического связывания, как в некоторых других ORM (например, TypeORM), но всё делается **явно** и **типобезопасно**.

Drizzle использует понятие `foreignKey`, `references`, `relations` и `sql`.

___

### 1\. **One-to-Many** (например: User -> Posts)

```
// tables/users.ts
import { pgTable, serial, text } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name"),
});
```

```
// tables/posts.ts
import { pgTable, serial, text, integer } from "drizzle-orm/pg-core";
import { users } from "./users";

export const posts = pgTable("posts", {
  id: serial("id").primaryKey(),
  title: text("title"),
  userId: integer("user_id").references(() => users.id), // foreign key
});
```

**Примечание:** `references(() => users.id)` создаёт внешний ключ на поле `id` в таблице `users`.

___

### 2\. **One-to-One**

То же, что и One-to-Many, но с уникальным ограничением:

```
export const profiles = pgTable("profiles", {
  id: serial("id").primaryKey(),
  bio: text("bio"),
  userId: integer("user_id").references(() => users.id).unique(),
});
```

___

### 3\. **Many-to-Many** (через промежуточную таблицу)

```
// tables/posts_tags.ts
import { pgTable, integer } from "drizzle-orm/pg-core";
import { posts } from "./posts";
import { tags } from "./tags";

export const postsToTags = pgTable("posts_to_tags", {
  postId: integer("post_id").references(() => posts.id),
  tagId: integer("tag_id").references(() => tags.id),
});
```

___

## 🔁 Определение `relations` (для выборок)

Для связей можно явно указать `relations` при помощи `drizzle-orm` API:

```
import { relations } from "drizzle-orm";
import { users } from "./users";
import { posts } from "./posts";

export const userRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

export const postRelations = relations(posts, ({ one }) => ({
  user: one(users, {
    fields: [posts.userId],
    references: [users.id],
  }),
}));
```

Это позволяет использовать `db.query.users.findMany({ with: { posts: true } })` для типобезопасных JOIN'ов.

___

## ✅ Итого: Порядок действий

1.  Создаёте таблицы с `pgTable`.
2.  Добавляете `references(() => table.field)` в нужные колонки.
3.  Определяете связи через `relations(...)`.
4.  Используете `db.query.table.findMany({ with: { ... } })` для загрузки связанных данных.
