
> Документация библиотеки next-auth:
https://next-auth.js.org/getting-started/example

---
Документация библиотеки next-auth: [https://next-auth.js.org/getting-started/example](https://next-auth.js.org/getting-started/example)

## 📌 Основы NextAuth.js

### 1\. ✅ **Интеграция через App Router**

NextAuth.js в Next.js 15 работает через **Route Handlers**.

Пример конфигурации:

```
// app/api/auth/[...nextauth]/route.ts
import NextAuth from "next-auth"
import Google from "next-auth/providers/google"

const handler = NextAuth({
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),
  ],
  // другие опции (см. ниже)
})

export { handler as GET, handler as POST }
```

___

### 2\. 🔐 **Конфигурация NextAuth**

Объект конфигурации включает:

-   `providers` — OAuth, Email, Credentials и др.
-   `callbacks` — кастомизация JWT, session, redirect и т.д.
-   `session.strategy` — `"jwt"` или `"database"`
-   `adapter` — подключение к базе данных (например, Drizzle)
-   `events`, `theme`, `cookies`, `debug`, и др.

___

### 3\. 🔑 **JWT по умолчанию**

Если не указан адаптер, используется стратегия `"jwt"`.

```
session: {
  strategy: "jwt",
},
```

JWT автоматически шифруется и хранится в cookie.

___

### 4\. 🧬 **Callbacks**

Позволяют кастомизировать поведение авторизации.

Примеры:

```
callbacks: {
  async jwt({ token, user }) {
    if (user) token.id = user.id
    return token
  },
  async session({ session, token }) {
    session.user.id = token.id
    return session
  }
}
```

___

### 5\. 🧩 **Adapter (например, Drizzle)**

Адаптер нужен для сохранения пользователей и сессий в базе данных.

Пример подключения:

```
import { DrizzleAdapter } from "@auth/drizzle-adapter"
import { db } from "@/lib/db"

adapter: DrizzleAdapter(db),
```

___

### 6\. 📤 **Клиентский доступ к сессии**

```
import { useSession, signIn, signOut } from "next-auth/react"

const { data: session } = useSession()

if (session) {
  return <button onClick={() => signOut()}>Выйти</button>
}
return <button onClick={() => signIn("google")}>Войти</button>
```

___

### 7\. 📄 **Переменные окружения**

```
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
NEXTAUTH_SECRET=...
NEXTAUTH_URL=http://localhost:3000
```

___

### 9\. 📦 **Установка**

___

## 🔐 Серверный доступ к сессии в App Router (`app/`)

### ✅ 1. Используй `auth()` из `@auth/nextjs`

Это обёртка над `getServerSession`, совместимая с App Router и Route Handlers.

```
// app/lib/auth.ts
import { getServerSession } from "next-auth"
import { authOptions } from "@/auth/options"

export async function auth() {
  return await getServerSession(authOptions)
}
```

Или импортируй напрямую из `@auth/nextjs`:

```
// app/lib/auth.ts
export { auth } from "@auth/nextjs"
```

___

### 🧠 Пример использования в серверном компоненте

```
// app/dashboard/page.tsx
import { auth } from "@/lib/auth"
import { redirect } from "next/navigation"

export default async function DashboardPage() {
  const session = await auth()

  if (!session) {
    redirect("/api/auth/signin")
  }

  return (
    <div>
      <h1>Добро пожаловать, {session.user?.name}</h1>
    </div>
  )
}
```

___

### ✅ 2. В middleware (опционально)

Для глобальной проверки:

```
// middleware.ts
import { auth } from "@auth/nextjs"
import { NextResponse } from "next/server"

export async function middleware(req: Request) {
  const session = await auth()

  if (!session) {
    return NextResponse.redirect(new URL("/api/auth/signin", req.url))
  }

  return NextResponse.next()
}
```

___

### ⚠️ Важно

-   `auth()` должен вызываться **в серверных компонентах** или **в route handlers**.
-   Не использовать в `useEffect` или клиентских хук-компонентах — используйте `useSession()` там.
