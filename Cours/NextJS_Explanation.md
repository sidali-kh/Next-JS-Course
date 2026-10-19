## **NextJS introduction**

- Next.js is a web framework for building full-stack web applications. 
- We use React Components to build user interfaces.
- Next.js runs on Node.js and provides server-side functionality. It can generate HTML on the server and send it to the client. At the same time, we can also specify which parts of the application should run on the server and which should run on the client.
- Client code run one time on the server and another time on the client.

## **Installation**

- Install Next.js using the following command: 


```python
npx create next-app@latest my-next-app
```

- Run the following command to start the development server:


```python
npm run dev
```

- Run the following command to build the application:


```python
npm run build
```

## **Routing in Next.js**

Next.js uses **file-system-based routing**, which means that routes are created based on the structure of files and directories inside the `app` directory.

### **1. The `app` Directory**

- The `app` directory is the root of the application's routing system and represents the `/` path.

- **Folder structure**

```text
app/
└── page.tsx
```


```python
%  `app/page.tsx`

export default function HomePage() {
  return <h1>Home Page</h1>;
}

% URL : http://localhost:3000/
```


### **2. Creating Routes**

- Directories inside the `app` directory represent URL segments.
- **Folder structure**

```text
app/
├── page.tsx
└── posts/
    └── page.tsx
```


```python
%  `app/posts/page.tsx`

export default function PostsPage() {
  return <h1>Posts Page</h1>;
}

% URL : http://localhost:3000/posts
```

- The `page.tsx` file is required for a directory to become a routable page.

### **3. Dynamic Routes**

- You can create a **dynamic route** by wrapping a directory name in square brackets.
- **Folder structure**

```text
app/
└── posts/
    └── [id]/
        └── page.tsx
```


```python
%  `app/posts/[id]/page.tsx`

export default async function PostPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  return <h1>Post ID: {id}</h1>;
}

% URL : http://localhost:3000/posts/42
% Result: Post ID: 42
```

All of these URLs will use the same page:

```text
http://localhost:3000/posts/1
http://localhost:3000/posts/2
http://localhost:3000/posts/100
```

### **4. Route Groups**

- A **route group** is created by wrapping a folder name in parentheses:

```text
(folder)
```

- The folder name is **not included in the URL**.
- **Folder structure**

```text
app/
├── (auth)/
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
└── page.tsx
```


```python
%  `app/(auth)/login/page.tsx`

export default function LoginPage() {
  return <h1>Login Page</h1>;
}

% URL : http://localhost:3000/login


%  `app/(auth)/register/page.tsx`

export default function RegisterPage() {
  return <h1>Register Page</h1>;
}

% URL : http://localhost:3000/register
```

Not:

```text
/auth/login
/auth/register
```

Route groups are useful for **organizing routes** and sharing layouts without affecting the URL structure.

### **5. Private Folders**

- A **private folder** starts with an underscore:

```text
_folder
```
- Private folders are not treated as route segments.
- **Folder structure**

```text
app/
├── _components/
│   └── Header.tsx
├── posts/
│   └── page.tsx
└── page.tsx
```


```python
% `app/_components/Header.tsx`

export default function Header() {
  return <header>My Header</header>;
}

% You can use this component from another page:

import Header from "../_components/Header";

export default function PostsPage() {
  return (
    <>
      <Header />
      <h1>Posts</h1>
    </>
  );
}
```

- There is **no route** such as:

    ```text
    /_components
    ```

- Private folders are useful for storing implementation details such as components and utilities that should not be part of the routing system.

### **6. Catch-All Route Segments**

- A **catch-all route** is created using three dots:

```text
[...folder]
```

- It matches **one or more URL segments**.
- **Folder structure**

```text
app/
└── docs/
    └── [...slug]/
        └── page.tsx
```


```python
% `app/docs/[...slug]/page.tsx`

export default async function DocsPage({
  params,
}: {
  params: Promise<{ slug: string[] }>;
}) {
  const { slug } = await params;

  return (
    <div>
      <h1>Documentation</h1>
      <p>Path: {slug.join(" / ")}</p>
    </div>
  );
}
```

- The route can match:

```text
http://localhost:3000/docs/intro
http://localhost:3000/docs/getting-started
http://localhost:3000/docs/react/components
```

For example:

```text
/docs/react/components
```

produces:

```js
{
  slug: ["react", "components"]
}
```

- **Important**
    - A regular catch-all route requires **at least one segment**.
        Therefore:
        ```text
        /docs/react          ✅
        /docs/react/hooks    ✅
        /docs                 ❌
        ```
- The `/docs` URL is not matched by `[...slug]`.

### **7. Optional Catch-All Route Segments**

- An **optional catch-all route** is created using double square brackets:

```text
[[...folder]]
```
- It can match **zero or more URL segments**.
- **Folder structure**

```text
app/
└── docs/
    └── [[...slug]]/
        └── page.tsx
```


```python
% `app/docs/[[...slug]]/page.tsx`

export default async function DocsPage({
  params,
}: {
  params: Promise<{ slug?: string[] }>;
}) {
  const { slug } = await params;

  return (
    <div>
      <h1>Documentation</h1>
      <p>
        {slug
          ? `Path: ${slug.join(" / ")}`
          : "Documentation home"}
      </p>
    </div>
  );
}
```

- This route can match:

  ```text
  http://localhost:3000/docs
  http://localhost:3000/docs/intro
  http://localhost:3000/docs/react
  http://localhost:3000/docs/react/components
  ```

**Example 01:**
- For:

  ```text
  /docs
  ```

- the value of `slug` is:

  ```js
  undefined
  ```

**Example 02:**

- For:

  ```text
  /docs/react/components
  ```

  the value is:

  ```js
  {
    slug: ["react", "components"]
  }
  ```

### **8. Using `Link` for Navigation**

For internal navigation, it is preferable to use Next.js's **`<Link>`** component instead of the standard HTML `<a>` tag.


```python
import Link from "next/link";

export default function HomePage() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/posts">Posts</Link>
      <Link href="/docs">Docs</Link>
    </nav>
  );
}
```

- `<Link>` enables client-side navigation and avoids a full page reload when navigating between pages.

### **9.Programmatic Navigation in Next.js**

Next.js provides several ways to navigate between routes programmatically.

The three important APIs are:

* `router.push()`
* `router.replace()`
* `redirect()`

The first two are used in **Client Components**, while `redirect()` is primarily used for **server-side navigation**.

#### **1. `router.push()`**

`router.push()` is used to navigate to another route from a **Client Component**.

It is provided by the `useRouter()` hook from `next/navigation`.


```python
"use client";

import { useRouter } from "next/navigation";

export default function HomePage() {
  const router = useRouter();

  function handleClick() {
    router.push("/about");
  }

  return (
    <button onClick={handleClick}>
      Go to About
    </button>
  );
}
```

When `router.push()` is called:

```text
/home
   ↓
router.push("/about")
   ↓
/about
```

The new route is added to the browser's history.

Therefore, the user can click the browser's **Back** button to return to `/home`.

#### **2. `router.replace()`**

`router.replace()` is also used in a **Client Component**, but unlike `push()`, it **replaces the current browser history entry**.


```python
"use client";

import { useRouter } from "next/navigation";

export default function LoginPage() {
  const router = useRouter();

  function handleLogin() {
    // Login logic...

    router.replace("/dashboard");
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

Suppose the user is currently at:

```text
/login
```

Then:

```tsx
router.replace("/dashboard");
```

changes the current history entry to:

```text
/dashboard
```

Instead of:

```text
/login → /dashboard
```

Therefore, the `/login` entry created by the current navigation is not available through the browser's Back button.

#### **3. `redirect()`**

`redirect()` is used for **server-side redirection**.


```python
import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const user = await getUser();

  if (!user) {
    redirect("/login");
  }

  return <h1>Dashboard</h1>;
}
```

The server checks whether the user is authenticated.

If the user is not authenticated:

```text
Request /dashboard
       ↓
Server checks user
       ↓
    No user
       ↓
redirect("/login")
       ↓
/login
```

If the user is authenticated:

```text
Request /dashboard
       ↓
Server checks user
       ↓
     User exists
       ↓
Render dashboard
```

Useful when the server determines that the user should not access the current route.
This makes `redirect()` particularly useful for:

* Authentication checks
* Authorization checks
* Server-side conditions
* Redirecting after server-side operations

## **Layouts in Next.js**

- A **layout** is a UI component that is shared between multiple pages. It is useful for elements that should remain visible while navigating between pages, such as a **navbar, sidebar, footer, or common page structure**.

- In the Next.js App Router, layouts are created using a file named **`layout.tsx`**.

### **1. Root Layout**

The `app/layout.tsx` file is the **root layout** and is required in an App Router application.

```text
app/
├── layout.tsx
├── page.tsx
├── about/
│   └── page.tsx
└── posts/
    └── page.tsx
```



```python
% `app/layout.tsx`

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <nav>
          <h1>My Website</h1>
        </nav>

        {children}

        <footer>
          <p>My Footer</p>
        </footer>
      </body>
    </html>
  );
}
```

The `children` prop represents the page or nested layout that is currently being displayed.

For example:

```text
/app/layout.tsx
       │
       ├── /page.tsx
       ├── /about/page.tsx
       └── /posts/page.tsx
```

All of these pages are rendered inside the root layout:

```text
┌─────────────────────────┐
│         Navbar          │
├─────────────────────────┤
│                         │
│       {children}        │
│                         │
├─────────────────────────┤
│         Footer          │
└─────────────────────────┘
```

### **2. Nested Layouts**

You can create a `layout.tsx` inside a route directory.

For example:

```text
app/
├── layout.tsx
├── page.tsx
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    ├── users/
    │   └── page.tsx
    └── settings/
        └── page.tsx
```

The dashboard layout applies to the dashboard page and all of its nested routes.


```python
% `app/dashboard/layout.tsx`

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <aside>
        <h2>Dashboard</h2>
        <nav>
          <a href="/dashboard">Home</a>
          <a href="/dashboard/users">Users</a>
          <a href="/dashboard/settings">Settings</a>
        </nav>
      </aside>

      <main>{children}</main>
    </div>
  );
}
```

When visiting:

```text
/dashboard
/dashboard/users
/dashboard/settings
```

the corresponding page is rendered inside the dashboard layout.
So:

```tsx
<RootLayout>
  <DashboardLayout>
    <DashboardPage />
  </DashboardLayout>
</RootLayout>
```

## **Metadata in Next.js**

- Metadata provides information about a web page that is not part of the visible page content. It is mainly used to describe the page to search engines and crawlers.

- Common metadata includes:

    * Page title
    * Page description
    * Keywords
    * Open Graph information
    * Twitter/X card information
    * Icons and favicons
    * Robots instructions
    * Canonical URLs

- Where Can Metadata Be Defined?
    - Metadata can be exported from:
        * `layout.tsx`
        * `page.tsx`

### **1. Static Metadata**

Static metadata is useful when the metadata does not depend on dynamic information.

Next.js provides the `Metadata` type from `next`:


```python
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "My Website",
  description: "A website built with Next.js",
};

export default function HomePage() {
  return <h1>Home Page</h1>;
}
```

The metadata is automatically used to generate the appropriate HTML elements, such as:

```html
<title>My Website</title>
<meta
  name="description"
  content="A website built with Next.js"
/>
```

### **2. Dynamic Metadata**

Sometimes metadata depends on dynamic route data.

For example:

```text
app/
└── products/
    └── [id]/
        └── page.tsx
```

Suppose `/products/123` displays product `123`.

The title should ideally contain the product's name. Since the information is dynamic, static `metadata` is not enough.

Next.js provides `generateMetadata` for this purpose:


```python
import type { Metadata } from "next";

type Props = {
  params: Promise<{ id: string }>;
};

export async function generateMetadata({
  params,
}: Props): Promise<Metadata> {
  const { id } = await params;

  const product = await getProduct(id);

  return {
    title: product.name,
    description: product.description,
  };
}

export default async function ProductPage({ params }: Props) {
  const { id } = await params;

  const product = await getProduct(id);

  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
    </div>
  );
}
```

Here, the metadata is generated based on the product being requested.

For example:

```text
/products/123
```

could generate:

```html
<title>iPhone 17 | My Store</title>
<meta
  name="description"
  content="Discover the iPhone 17."
/>
```

## **CSS Modules in Next.js**

It is preferable to use **CSS Modules** instead of regular CSS for component-specific styling because CSS Modules automatically scope class names to the component, helping avoid naming conflicts.

To use CSS Modules, add **`.module`** to the CSS file name:

```text
styles.module.css
```


```python
/* Create a CSS Module: */

/* styles.module.css */

.title {
  color: blue;
  font-size: 2rem;
}

```


```python
% Then import it into your component:

import styles from "./styles.module.css";

export default function Home() {
  return (
    <div>
      <h1 className={styles.title}>Hello World</h1>
    </div>
  );
}
```

- Next.js will generate a unique class name for the `.title` class, preventing it from conflicting with other `.title` classes used elsewhere in the application.

## **Server and Client Components**

By default, **components in the Next.js App Router are Server Components**.

### **Server Components**

Server Components run on the server, so they cannot use client-only features such as:

* React hooks such as `useState` and `useEffect`
* Event handlers such as `onClick` and `onChange`
* Browser APIs such as `window` and `localStorage`

- If a component needs these features, add the `"use client"` directive at the top of the file:



```python
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**Note :**
- Avoid adding `"use client"` to a component unless it actually needs client-side features.
- Keeping components as Server Components when possible can reduce the amount of JavaScript that needs to be sent to the browser.

### **Client Components Also Render on the Server**

A common misconception is that Client Components **only run in the browser**.

Client Components are initially **rendered on the server** to generate the initial UI. Their JavaScript is then sent to the browser, where the component becomes interactive through **hydration**.

A simplified process is:

```text
Server
  │
  ├── Render Client Component
  │
  ├── Generate initial UI
  │
  └── Send HTML + JavaScript
             │
             ▼
          Browser
             │
             └── Hydration
                 ↓
             Interactive UI
```

Therefore, `"use client"` does **not** mean "this component only executes on the client." It means that the component is part of the **client component tree** and can use client-side features such as state, effects, and event handlers.

## **Handling Errors**

- You can create an error.tsx file to handle errors that occur during rendering or data fetching in a route segment.

- The error.tsx file must be a Client Component, so you need to add the "use client" directive at the top of the file.

- The file can be placed inside the relevant route segment or at the root of the `app` directory to provide a custom error page.


```python
% app/error.tsx 

"use client";
const Error = ({ error : Error, reset : () => void }) => {
    return (
        <div>
            <h1>Error</h1>
            <p>{error.status}</p>
            <p>{error.message}</p>
            % reset used to rerender the page
            <button onClick={() => reset()}>Reset</button>     
        </div>
    )
}
```

## **Handling 404 Errors**
- You can create a **`not-found.tsx`** file to define a custom UI for **404 (Not Found) errors** in Next.js.

- The file can be placed inside the relevant route segment or at the root of the `app` directory to provide a custom not-found page.


```python
% app/not-found.tsx

export default function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <p>The page you are looking for does not exist.</p>
    </div>
  );
}
```

## **Loading UI in Next.js**

- Next.js provides a special `loading.tsx` file for displaying a **loading UI while the content of a route segment is being rendered or loaded**.
- It is especially useful when a page performs asynchronous operations such as fetching data.

- Create a `loading.tsx` file inside a route segment and can also be placed inside a specific route segment. Therefore, The closest applicable loading boundary can provide the loading UI for that part of the route.

```text
app/
├── layout.tsx
├── page.tsx
├── loading.tsx
└── posts/
    ├── page.tsx
    └── loading.tsx
```


```python
% app/loading.tsx

export default function Loading() {
  return <p>Loading...</p>;
}
```

- When the home route is loading, Next.js can display this UI before the page content becomes available.

### **Loading UI and Suspense**

Next.js uses React's **Suspense** mechanism to implement loading states.

A `loading.tsx` file is effectively a convenient way to create a loading boundary around a route segment.

Conceptually:

```tsx
<Suspense fallback={<Loading />}>
  <Page />
</Suspense>
```

You normally do **not** need to manually write this Suspense wrapper when using `loading.tsx`.

Next.js handles the route-level loading boundary for you.


## **Using Images in Next.js**

It is preferable to use the **`Image` component provided by Next.js** instead of the standard HTML `<img>` tag. The `Image` component provides built-in image optimization, such as resizing and compression, which can help improve page loading performance.

You can also specify the **width** and **height** of the image:


```python
import Image from "next/image";

<Image
  src="/images/example.jpg"
  width={500}
  height={300}
  alt="Example"
/>
```

- When using a **local image**, Next.js can often determine its dimensions automatically when the image is imported.

- However, when the image comes from an **external URL**, you must specify the `width` and `height` manually. You must also configure the external domain in `next.config.js` so that Next.js allows the image to be loaded.

For example:


```python
% next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "example.com",
        port: "",
        pathname: "/**",
      },
    ],
  },
};
```

- After configuring `next.config.js`, you can use the external image with the `Image` component:


```python
import Image from "next/image";

<Image
  src="https://example.com/image.jpg"
  width={800}
  height={600}
  alt="Example"
/>
```

## **Data Caching in Next.js**

**Data caching** in Next.js allows the result of a data request to be stored and reused, so the application does not need to fetch the same data again on every request.

In the **App Router**, Next.js can cache data fetched with `fetch()`.

### **1. Cached Data**

You can explicitly cache a request using:


```python
const response = await fetch("https://api.example.com/posts", {
  cache: "force-cache",
});
% or just as this since the chache is the default behavior
const response = await fetch("https://api.example.com/posts");
```

- The result can then be reused by Next.js instead of making a new request each time.

### **2. Dynamic Data**

If you always want fresh data, use:


```python
const response = await fetch("https://api.example.com/posts", {
  cache: "no-store",
});
```

- This tells Next.js **not to cache the request**.

### **3. Revalidate Cached Data**

You can also cache the data but periodically refresh it:


```python
const response = await fetch("https://api.example.com/posts", {
  next: {
    revalidate: 60,
  },
});
```

- Here, `60` means that the cached data can be revalidated after **60 seconds**.

## **Prisma ORM**

**Prisma** is a TypeScript-friendly ORM (Object-Relational Mapper) that allows a Next.js application to work with a relational database using TypeScript/JavaScript instead of writing SQL for every operation.

Prisma provides:

* A schema for defining database models
* Type-safe database queries
* Database migrations
* CRUD operations
* Relationships between models
* Automatic TypeScript types
* A generated Prisma Client for communicating with the database.
* Prisma Studio provides a graphical interface where you can inspect your models and records.
``` prisma
npx prisma studio
```

Prisma does **not** replace the database.

For example, you can use Prisma with:

* PostgreSQL
* MySQL
* SQLite
* SQL Server
* CockroachDB
* MongoDB

The database still stores the actual data. Prisma provides the interface through which your application communicates with it.

### **1. Installing Prisma**

Inside a Next.js project, install Prisma:

```bash
npm install prisma @prisma/client
```

Then initialize Prisma:

```bash
npx prisma init
```

This creates a Prisma directory:

```text
project/
├── app/
├── prisma/
│   └── schema.prisma
├── .env
├── package.json
└── ...
```

The two important files are:

```text
prisma/schema.prisma
.env
```


### **2. The Prisma Schema**

The Prisma schema is where you define:

* The database provider
* Database connection
* Models
* Fields
* Relationships
* Constraints

A basic schema:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

The `User` model represents a table in a relational database.

Conceptually:

```text
Prisma Model
     ↓
Database Table

User
     ↓
users
```

The exact database table name can be customized when necessary.


### **3. Database Connection**

The connection URL is normally stored in `.env`.

For example:

```env
DATABASE_URL="postgresql://username:password@localhost:5432/mydatabase"
```

The Prisma schema accesses it using:

```prisma
url = env("DATABASE_URL")
```

Do not hard-code database credentials directly into your source code.

The `.env` file should also not be committed to a public Git repository.


### **4. Prisma Client**

Prisma Client is the generated library used by your application to communicate with the database.

After defining your schema, generate Prisma Client:

```bash
npx prisma generate
```

You can then import it:

```tsx
import { PrismaClient } from "@prisma/client";
```

and create a client:

```tsx
const prisma = new PrismaClient();
```

However, in a Next.js development environment, creating a new `PrismaClient` every time a module is reloaded can lead to multiple database connections.

Therefore, a common Next.js pattern is to create a shared Prisma instance.

### **5. Creating a Shared Prisma Client**

Create:

```text
src/
└── lib/
    └── prisma.ts
```

For example:

```tsx
// src/lib/prisma.ts

import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient();

if (process.env.NODE_ENV !== "production") {
  globalForPrisma.prisma = prisma;
}
```

Now other server-side files can import the same instance:

```tsx
import { prisma } from "@/lib/prisma";
```

This pattern helps avoid creating unnecessary Prisma Client instances during Next.js development hot reloads.

### **6. Creating Database Tables with Migrations**

run:

```bash
npx prisma migrate dev --name create_user
```

Prisma creates a migration and updates the database.

Your project will contain something similar to:

```text
prisma/
├── schema.prisma
└── migrations/
    └── 20261003120000_create_user/
        └── migration.sql
```

The migration records the database changes.


Prisma provides two common ways to synchronize the schema with a database.

* `prisma migrate dev` : Generates and applies migrations
* `prisma db push` : Pushes the Prisma schema directly to the database without creating migration files

### **8. Creating Data**

Suppose we have:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

We can create a user:

```tsx
const user = await prisma.user.create({
  data: {
    name: "Sidali",
    email: "sidali@example.com",
  },
});
```

The returned object contains the created record:

```tsx
console.log(user);
```

For example:

```json
{
  "id": 1,
  "name": "Sidali",
  "email": "sidali@example.com"
}
```

### **9. Reading Data**

To retrieve all users:

```tsx
const users = await prisma.user.findMany();
```

To retrieve a single user:

```tsx
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
});
```

To retrieve the first user:

```tsx
const user = await prisma.user.findFirst({
  where: {
    id: 1,
  },
});
```

You can also search using another unique field:

```tsx
const user = await prisma.user.findUnique({
  where: {
    email: "sidali@example.com",
  },
});
```

### **10. Filtering Data**

Prisma allows you to filter records.

For example:

```tsx
const users = await prisma.user.findMany({
  where: {
    name: "Sidali",
  },
});
```

You can also use conditions:

```tsx
const users = await prisma.user.findMany({
  where: {
    name: {
      contains: "Ali",
    },
  },
});
```

### **11. Selecting Specific Fields**

By default, Prisma returns the fields selected by the query.

You can explicitly select fields:

```tsx
const users = await prisma.user.findMany({
  select: {
    id: true,
    name: true,
  },
});
```

The result does not contain `email`.

```json
[
  {
    "id": 1,
    "name": "Sidali"
  }
]
```

This can be useful when you don't need every field.

### **12. Updating Data**

To update a record:

```tsx
const user = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: "New Name",
  },
});
```

You can update multiple fields:

```tsx
const user = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: "New Name",
    email: "new@example.com",
  },
});
```

For partial updates, you only need to include the fields that should change.

For example:

```json
{
  "name": "KitKat"
}
```

can update only the `name` field without modifying other fields.

```tsx
const user = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: "KitKat",
  },
});
```

### **13. Deleting Data**

To delete one record:

```tsx
const user = await prisma.user.delete({
  where: {
    id: 1,
  },
});
```

To delete multiple records:

```tsx
const result = await prisma.user.deleteMany({
  where: {
    name: "Sidali",
  },
});
```

Be careful with `deleteMany()` because it can affect multiple records.

### **14. CRUD with Prisma**

The basic Prisma CRUD methods are:

```text
CREATE
    prisma.user.create()

READ
    prisma.user.findUnique()
    prisma.user.findMany()

UPDATE
    prisma.user.update()
    prisma.user.updateMany()

DELETE
    prisma.user.delete()
    prisma.user.deleteMany()
```

This gives you the basic database operations required by most applications.


### **15. Relationships**

One of Prisma's important features is defining relationships between models.

For example, suppose a user can write many posts:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique

  posts Post[]
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  content  String?

  author   User @relation(fields: [authorId], references: [id])
  authorId Int
}
```

This represents:

```text
User
 │
 │ 1
 │
 │
 │ *
 ▼
Post
```

One user can have many posts.

The `authorId` field stores the relationship in the `Post` table.


### **16. Creating Related Data**

You can create a post and connect it to an existing user:

```tsx
const post = await prisma.post.create({
  data: {
    title: "My First Post",
    content: "Hello world!",

    author: {
      connect: {
        id: 1,
      },
    },
  },
});
```

The post is associated with user `1`.

### **17. Including Related Data**

You can retrieve a user together with their posts:

```tsx
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },

  include: {
    posts: true,
  },
});
```

The result can contain:

```json
{
  "id": 1,
  "name": "Sidali",
  "email": "sidali@example.com",
  "posts": [
    {
      "id": 1,
      "title": "My First Post"
    }
  ]
}
```

This is useful when working with relational data.

### **18. Prisma Studio**

Prisma also provides **Prisma Studio**, a visual interface for viewing and editing database records.

Run:

```bash
npx prisma studio
```

Prisma Studio provides a graphical interface where you can inspect your models and records.

Conceptually:

```text
Prisma Schema
      ↓
   Database
      ↑
Prisma Studio
```

It is particularly useful during development for inspecting whether your CRUD operations are working correctly.

### **19. Prisma and TypeScript**

One of Prisma's major advantages is type safety.

For example:

```tsx
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
});
```

Prisma knows the fields defined in the `User` model.

If you try to use an invalid field:

```tsx
const user = await prisma.user.findUnique({
  where: {
    username: "Sidali",
  },
});
```

TypeScript/Prisma can report that `username` is not a valid field if it is not defined in the schema.

This reduces many database-related mistakes during development.

## **API in Next.js**

Next.js allows you to build **API endpoints directly inside your application** using **Route Handlers**.

A Route Handler lets you create server-side endpoints that can receive HTTP requests and return HTTP responses.

For example, you can create endpoints for:

* Getting data
* Creating records
* Updating records
* Deleting records
* Authentication
* File processing
* Database operations
* Calling external APIs securely

In the App Router, Route Handlers are created using a special file named:

```text
route.ts
```

or:

```text
route.js
```

### **1. Creating an API Route**

A Route Handler is defined inside the `app` directory.

For example:

```text
app/
├── page.tsx
└── api/
    └── users/
        └── route.ts
```

This creates the API endpoint:

```text
/api/users
```

The `route.ts` file can define HTTP methods such as:

* `GET`
* `POST`
* `PUT`
* `PATCH`
* `DELETE`
* `HEAD`
* `OPTIONS`

Example:



```python
// app/api/users/route.ts

export async function GET() {
  return Response.json({
    message: "Hello from the API",
  });
}
```

Now a request to:

```text
GET /api/users
```

returns:

```json
{
  "message": "Hello from the API"
}
```

### **2. HTTP Methods**

#### **1. Create — `POST`**

```ts
import { NextRequest, NextResponse } from "next/server";
import { prisma } from "@/utils/db";

export async function POST(request: NextRequest) {
  const data = await request.json();

  const article = await prisma.article.create({
    data,
  });

  return NextResponse.json(article, { status: 201 });
}
```

#### **2. Get one article — `GET`**

File:

```text
app/api/articles/[id]/route.ts
```

```ts
import { NextRequest, NextResponse } from "next/server";
import { prisma } from "@/utils/db";

export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;

  const article = await prisma.article.findUnique({
    where: {
      id: Number(id),
    },
  });

  if (!article) {
    return NextResponse.json(
      { message: "Article not found" },
      { status: 404 }
    );
  }

  return NextResponse.json(article);
}
```

#### **3. Get all articles — `GET`**

File:

```text
app/api/articles/route.ts
```

```ts
export async function GET() {
  const articles = await prisma.article.findMany();

  return NextResponse.json(articles);
}
```

#### **4. Update — `PATCH`**

```ts
export async function PATCH(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  const data = await request.json();

  const article = await prisma.article.update({
    where: {
      id: Number(id),
    },
    data,
  });

  return NextResponse.json(article);
}
```

#### **5. Delete — `DELETE`**

```ts
export async function DELETE(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;

  await prisma.article.delete({
    where: {
      id: Number(id),
    },
  });

  return NextResponse.json({
    message: "Article deleted successfully",
  });
}
```

### **Practicle example Auth System**

#### **Generate JWT Token**

`src/utils/generateToken.ts`

```tsx
import jwt from 'jsonwebtoken';
import { serialize } from 'cookie';

type JWTPayload = {
    id: number;
    isAdmin: boolean;
    username: string;
}
// Genereate JWT Token
export function generateJWT(jwtPayload: JWTPayload): string {
    const privateKey = process.env.JWT_SECRET as string;

    const token = jwt.sign(jwtPayload, privateKey, {
        expiresIn: '30d'
    });

    return token;
}

// Set Cookie with JWT
export function setCookie(jwtPayload: JWTPayload): string {
    const token = generateJWT(jwtPayload);

    const cookie = serialize("jwtToken", token, {
        httpOnly: true,
        secure: process.env.NODE_ENV === 'production', // development=http, production= https
        sameSite: 'strict',
        path: '/',
        maxAge: 60 * 60 * 24 * 30, // 30 days
    });

    return cookie;
}

```

#### **Verify JWT Token**

`src/utils/verifyToken.ts`

```tsx
import jwt from 'jsonwebtoken';
import { NextRequest } from 'next/server';
import { JWTPayload } from '@/utils/types';

// Verify Token For API End Point
export function verifyToken(request: NextRequest): JWTPayload | null {
    try {
        const jwtToken = request.cookies.get("jwtToken");
        const token = jwtToken?.value as string;
        if (!token) return null;

        const privateKey = process.env.JWT_SECRET as string;
        const userPayload = jwt.verify(token, privateKey) as JWTPayload;

        return userPayload;
    } catch (error) {
        return null;
    }
}

// Verify Token For Page
export function verifyTokenForPage(token: string): JWTPayload | null {
    try {
        const privateKey = process.env.JWT_SECRET as string;
        const userPayload = jwt.verify(token, privateKey) as JWTPayload;
        if(!userPayload) return null;

        return userPayload;
    } catch (error) {
        return null;
    }
}
```

#### **Validation Schemas**

`src/utils/validationSchemas.ts`

```tsx
import { z } from 'zod';

// Register Schema
export const registerSchema = z.object({
    username: z.string().min(2).max(100), //.optional(),
    email: z.string().min(3).max(200).email(),
    password: z.string().min(6),
});

// Login Schema
export const loginSchema = z.object({
    email: z.string().min(3).max(200).email(),
    password: z.string().min(6),
});


// Update User Profile Schema
export const updateUserSchema = z.object({
    username: z.string().min(2).max(100).optional(),
    email: z.string().min(3).max(200).email().optional(),
    password: z.string().min(6).optional(),
});
```

#### **DTOs (Data Transfer Objects)**

`src/utils/dtos.ts`

```tsx
export interface RegisterUserDto {
    username: string;
    email: string;
    password: string;
}

export interface LoginUserDto {
    email: string;
    password: string;
}

export interface UpdateUserDto {
    username?: string;
    email?: string;
    password?: string;
}
```

#### **Register User**

`src/app/api/users/register/route.ts`

```tsx
import { RegisterUserDto } from '@/utils/dtos';
import { registerSchema } from '@/utils/validationSchemas';
import { NextResponse, NextRequest } from 'next/server';
import prisma from '@/utils/db';
import bcrypt from 'bcryptjs';
import { setCookie } from '@/utils/generateToken';

/**
 *  @method  POST
 *  @route   ~/api/users/register
 *  @desc    Create New User [(Register) (Sign Up) (انشاء حساب)]
 *  @access  public
 */
export async function POST(request: NextRequest) {
    try {
        const body = await request.json() as RegisterUserDto;
        const validation = registerSchema.safeParse(body);
        if (!validation.success) {
            return NextResponse.json({ message: validation.error.errors[0].message }, { status: 400 })
        }

        const user = await prisma.user.findUnique({ where: { email: body.email } });
        if (user) {
            return NextResponse.json(
                { message: 'this user already registered' },
                { status: 400 }
            );
        }

        const salt = await bcrypt.genSalt(10);
        const hashedPassword = await bcrypt.hash(body.password, salt);

        const newUser = await prisma.user.create({
            data: {
                username: body.username,
                email: body.email,
                password: hashedPassword,
            },
            select: {
                username: true,
                id: true,
                isAdmin: true,
            }
        });


        const cookie = setCookie({
            id: newUser.id,
            username: newUser.username,
            isAdmin: newUser.isAdmin,
        });

        return NextResponse.json(
            { ...newUser, message: "Registered & Authenticated" },
            {
                status: 201,
                headers: { "Set-Cookie": cookie }
            });

    } catch (error) {
        return NextResponse.json(
            { message: 'internal server error' },
            { status: 500 }
        );
    }
}
```

#### **Login User**

`src/app/api/users/login/route.ts`

```tsx
import { LoginUserDto } from '@/utils/dtos';
import { loginSchema } from '@/utils/validationSchemas';
import { NextResponse, NextRequest } from 'next/server';
import prisma from '@/utils/db';
import bcrypt from 'bcryptjs';
import { setCookie } from '@/utils/generateToken';


/**
 *  @method  POST
 *  @route   ~/api/users/login
 *  @desc    Login User [(Log In) (Sign In) (تسجیل الدخول)]
 *  @access  public
 */
export async function POST(request: NextRequest) {
    try {
        const body = await request.json() as LoginUserDto;
        const validation = loginSchema.safeParse(body);
        if (!validation.success) {
            return NextResponse.json(
                { message: validation.error.errors[0].message },
                { status: 400 }
            );
        }

        const user = await prisma.user.findUnique({ where: { email: body.email } });
        if (!user) {
            return NextResponse.json(
                { message: 'invalid email or password' },
                { status: 400 }
            )
        }

        const isPasswordMatch = await bcrypt.compare(body.password, user.password);
        if (!isPasswordMatch) {
            return NextResponse.json(
                { message: 'invalid email or password' },
                { status: 400 }
            );
        }

        const cookie = setCookie({
            id: user.id,
            isAdmin: user.isAdmin,
            username: user.username
        });

        return NextResponse.json(
            { message: 'Authenticated' },
            {
                status: 200,
                headers: { "Set-Cookie": cookie }
            }
        )

    } catch (error) {
        return NextResponse.json(
            { message: 'internal server error' },
            { status: 500 }
        )
    }
}
```

#### **Logout User**

`src/app/api/users/logout/route.ts`

```tsx
import { NextRequest, NextResponse } from 'next/server';
import { cookies } from 'next/headers';

/**
 *  @method  GET
 *  @route   ~/api/users/logout
 *  @desc    Logout User
 *  @access  public
 */
export function GET(request: NextRequest) {
    try {
        cookies().delete("jwtToken");
        return NextResponse.json({ message: 'logout' }, { status: 200 });
    } catch (error) {
        return NextResponse.json(
            { message: 'internal server error' },
            { status: 500 }
        )
    }
}
```

#### **CRUD User**

`src/app/api/users/profile/[id]/route.ts`

```tsx
import { NextResponse, NextRequest } from 'next/server';
import prisma from '@/utils/db';
import { verifyToken } from '@/utils/verifyToken';
import { UpdateUserDto } from '@/utils/dtos';
import bcrypt from 'bcryptjs';
import { updateUserSchema } from '@/utils/validationSchemas';

interface Props {
    params: { id: string };
}

/**
 *  @method  DELETE
 *  @route   ~/api/users/profile/:id
 *  @desc    Delete Profile
 *  @access  private (only user himself can delete his account)
 */
export async function DELETE(request: NextRequest, { params }: Props) {
    try {
        const user = await prisma.user.findUnique({ 
            where: { id: parseInt(params.id) },
            include: { comments: true }
        });
        if (!user) {
            return NextResponse.json(
                { message: 'user not found' },
                { status: 404 }
            );
        }

        const userFromToken = verifyToken(request);
        if (userFromToken !== null && userFromToken.id === user.id) {
            // deleting the user
            await prisma.user.delete({ where: { id: parseInt(params.id) } });
            return NextResponse.json(
                { message: 'your profile (account) has been deleted' },
                { status: 200 }
            );
        }

        return NextResponse.json(
            { message: 'only user himself can delete his profile, forbidden' },
            { status: 403 }
        )

    } catch (error) {
        return NextResponse.json(
            { message: 'internal server error' },
            { status: 500 }
        )
    }
}


/**
 *  @method  GET
 *  @route   ~/api/users/profile/:id
 *  @desc    Get Profile By Id
 *  @access  private (only user himself can get his account/profile)
 */
export async function GET(request: NextRequest, { params }: Props) {
  try {
    const user = await prisma.user.findUnique({
        where:{ id: parseInt(params.id) },
        select: {
            id: true,
            email: true,
            username: true,
            createdAt: true,
            isAdmin: true,
        }
    });

    if(!user) {
        return NextResponse.json({ message: 'user not found' }, { status: 404 });
    }

    const userFromToken = verifyToken(request);
    if(userFromToken === null || userFromToken.id !== user.id){
        return NextResponse.json(
            { message: 'you are not allowed, access denied' },
            { status: 403 }
        )
    }

    return NextResponse.json(user, { status: 200 });

  } catch (error) {
    return NextResponse.json(
        { message: 'internal server error' },
        { status: 500 }
    )
  }
}


/**
 *  @method  PUT
 *  @route   ~/api/users/profile/:id
 *  @desc    Update Profile
 *  @access  private (only user himself can update his account/profile)
 */
export async function PUT(request: NextRequest, { params } : Props) {
    try {
        const user = await prisma.user.findUnique({ where: { id: parseInt(params.id) }});
        if(!user) {
            return NextResponse.json({ message: 'user not found' }, { status: 404 });
        }

        const userFromToken = verifyToken(request);
        if(userFromToken === null || userFromToken.id !== user.id) {
            return NextResponse.json(
                { message: 'you are not allowed, access denied' },
                { status: 403 }
            )
        }

        const body = await request.json() as UpdateUserDto;
        const validation = updateUserSchema.safeParse(body);
        if(!validation.success) {
            return NextResponse.json(
                { message: validation.error.errors[0].message },
                { status: 400 }
            );
        }

        if(body.password) {
           const salt = await bcrypt.genSalt(10);
           body.password = await bcrypt.hash(body.password, salt); 
        }
        const updatedUser = await prisma.user.update({
            where: { id: parseInt(params.id) },
            data: {
                username: body.username,
                email: body.email,
                password: body.password
            }
        });

        const { password, ...other } = updatedUser;
        return NextResponse.json({ ...other }, { status: 200 });

    } catch (error) {
        return NextResponse.json(
            { message: 'internal server error' },
            { status: 500 }
        )
    }
}
```

### **Middleware in Next.js**

Middleware allows you to **run code before a request is completed**. It can inspect or modify the request and decide whether the user should continue, be redirected, or receive a different response.

It is commonly used for:

* Authentication and authorization
* Redirecting users
* Protecting routes
* Adding or checking headers
* Rewriting URLs
* Running logic before a page or API route

#### **1. Creating Middleware**

In a Next.js project, create a `middleware.ts` file at the project root:

```text
my-app/
├── app/
│   ├── page.tsx
│   └── dashboard/
│       └── page.tsx
├── middleware.ts
└── package.json
```

Example:

```ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  console.log("Middleware executed");

  return NextResponse.next();
}
```

`NextResponse.next()` allows the request to continue to its destination.


#### **2. Redirecting a User**

Middleware can redirect a user before the requested page is rendered.

```ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const isLoggedIn = false;

  if (!isLoggedIn) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}
```

If the user is not logged in, they are redirected to `/login`.

#### **3. Protecting Specific Routes**

By default, middleware can run for many requests. The `matcher` configuration allows you to specify which routes it should apply to.

```ts
export const config = {
  matcher: ["/dashboard/:path*"],
};
```

Complete example:

```ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const token = request.cookies.get("token");

  if (!token) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*"],
};
```

Now middleware protects:

```text
/dashboard
/dashboard/profile
/dashboard/settings
/dashboard/users
```

This is useful for checking whether an authentication cookie exists.

> Middleware can check authentication information, but the actual authorization logic should still be enforced on the server where sensitive data or actions are handled.

#### **4. Reading Query Parameters**

Middleware can access the URL:

```ts
const searchParams = request.nextUrl.searchParams;

const page = searchParams.get("page");
```

For example:

```text
/products?page=2
```

```ts
export function middleware(request: NextRequest) {
  const page = request.nextUrl.searchParams.get("page");

  console.log(page);

  return NextResponse.next();
}
```

#### **5. Rewriting a Request**

A rewrite changes where the request is handled **without changing the URL displayed in the browser**.

```ts
return NextResponse.rewrite(
  new URL("/maintenance", request.url)
);
```

For example:

```text
/user
```

can internally be handled by:

```text
/maintenance
```

while the browser still displays:

```text
/user
```

### Redirect vs Rewrite

| Feature                          | Redirect       | Rewrite          |
| -------------------------------- | -------------- | ---------------- |
| Browser URL changes              | Yes            | No               |
| User is sent to another URL      | Yes            | No               |
| Request handled by another route | Yes            | Yes              |
| Common use                       | Login redirect | Internal routing |


#### **6. Adding Headers**

Middleware can modify request or response headers.

Example:

```ts
import { NextResponse } from "next/server";

export function middleware(request: NextRequest) {
  const response = NextResponse.next();

  response.headers.set("x-app-version", "1.0");

  return response;
}
```


```python
!jupyter nbconvert --to html NextJS_Explanation.ipynb
```

    [NbConvertApp] Converting notebook NextJS_Explanation.ipynb to html
    [NbConvertApp] Writing 548575 bytes to NextJS_Explanation.html
    


```python
!jupyter nbconvert --to markdown NextJS_Explanation.ipynb
```
