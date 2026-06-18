# 🚀 Prisma Part 1: Introduction to Prisma (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের প্রথম পর্ব।

---

# 📖 Prisma কী?

**Prisma** হলো একটি Modern ORM (Object Relational Mapping) Tool যা Node.js এবং TypeScript অ্যাপ্লিকেশনকে Database-এর সাথে সহজে কাজ করতে সাহায্য করে।

সহজ ভাষায় বলতে গেলে, Prisma Database Query লেখাকে আরও সহজ, Type Safe এবং Developer Friendly করে তোলে।

---

# 🤔 ORM কী?

ORM (Object Relational Mapping) হলো Application এবং Database-এর মধ্যে একটি Bridge।

ORM ব্যবহার করলে সরাসরি SQL Query না লিখেও Database-এর সাথে কাজ করা যায়।

## Without ORM

```text
Application
    │
    ▼
 Raw SQL
    │
    ▼
 Database
```

## With Prisma ORM

```text
Application
    │
    ▼
 Prisma Client
    │
    ▼
 Database
```

Prisma Client JavaScript/TypeScript Code কে SQL Query-তে রূপান্তর করে।

---

# ❓ কেন Prisma ব্যবহার করবো?

Traditional Database Development-এ কিছু সাধারণ সমস্যা দেখা যায়:

### ❌ Raw SQL Complex

```sql
SELECT * FROM users;
```

প্রজেক্ট বড় হলে Query আরও জটিল হয়ে যায়।

### ❌ Type Safety নেই

```js
user.emial
```

ভুল Property Name ব্যবহার করলেও অনেক সময় Runtime পর্যন্ত Error ধরা পড়ে না।

### ❌ Auto-completion নেই

Database-এর Table এবং Column Name মনে রাখতে হয়।

### ❌ Migration Management কঠিন

Database Schema পরিবর্তন Track করা কঠিন হয়ে যায়।

---

# ✅ Prisma-এর সুবিধা

## 1. Type Safety

Prisma Compile Time-এ Error ধরতে পারে।

```prisma
model User {
  id    Int
  email String
}
```

```js
user.email
```

✔️ Valid

```js
user.emial
```

❌ Invalid

---

## 2. Auto Completion

VS Code-এ Prisma Client ব্যবহার করলে Auto Suggestion পাওয়া যায়।

```js
prisma.user.
```

Suggested Methods:

```text
findMany()
findUnique()
create()
update()
delete()
```

---

## 3. Easy Database Query

SQL:

```sql
SELECT * FROM users;
```

Prisma:

```js
await prisma.user.findMany();
```

---

## 4. Multiple Database Support

Prisma বিভিন্ন Database Support করে:

* PostgreSQL
* MySQL
* SQLite
* SQL Server
* MongoDB

---

## 5. Built-in Migration System

```bash
npx prisma migrate dev
```

Prisma স্বয়ংক্রিয়ভাবে Migration File Generate করতে পারে।

---

# 🏗️ Prisma Ecosystem

Prisma শুধুমাত্র ORM নয়, এটি একাধিক Tool-এর সমন্বয়।

```text
Prisma ORM
│
├── Prisma Schema
├── Prisma Client
├── Prisma Migrate
├── Prisma Studio
└── Prisma CLI
```

---

# 🔥 Prisma Components

## 1. Prisma Schema

Prisma Project-এর Heart।

ফাইল:

```text
schema.prisma
```

Example:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
}
```

---

## 2. Prisma Client

Database Query করার জন্য Generated Library।

Example:

```js
const users = await prisma.user.findMany();
```

---

## 3. Prisma Migrate

Database Schema Update করার Tool।

```bash
npx prisma migrate dev
```

Process:

```text
Schema
  ↓
Migration
  ↓
Database
```

---

## 4. Prisma Studio

Browser থেকে Database Manage করার GUI Tool।

Run:

```bash
npx prisma studio
```

Features:

* View Data
* Edit Data
* Delete Data
* Search Data

---

## 5. Prisma CLI

Prisma-এর Command Line Tool।

Common Commands:

```bash
npx prisma init
```

```bash
npx prisma generate
```

```bash
npx prisma migrate dev
```

---

# ⚙️ Prisma Architecture

```text
Your Application
       │
       ▼
 Prisma Client
       │
       ▼
 Query Engine
       │
       ▼
 Database
```

---

# 🔄 Request Flow Example

ধরো তুমি নিচের Query চালালে:

```js
await prisma.user.findMany();
```

### Step 1

Application Request পাঠাবে।

### Step 2

Prisma Client Request গ্রহণ করবে।

### Step 3

Prisma Query Engine SQL Generate করবে।

```sql
SELECT * FROM users;
```

### Step 4

Database Query Execute করবে।

### Step 5

Result JavaScript Object হিসেবে Return হবে।

```js
[
  {
    id: 1,
    name: "Yeasin"
  }
]
```

---

# ⚔️ Prisma vs Traditional ORM

| Feature               | Prisma    | Traditional ORM |
| --------------------- | --------- | --------------- |
| Type Safety           | ✅         | ❌               |
| Auto Completion       | ✅         | Limited         |
| Migration Tool        | ✅         | Varies          |
| Schema First Approach | ✅         | Mostly No       |
| Developer Experience  | Excellent | Medium          |

---

# 🌍 Real World Use Cases

Prisma ব্যবহার করা যায়:

* E-commerce Website
* School Management System
* Hospital Management System
* Grocery Delivery App
* Asset Management System
* Blog Platform
* SaaS Application

---

# 📌 Summary

Prisma হলো একটি Modern ORM যা:

* Database Query সহজ করে
* Type Safety প্রদান করে
* Auto Completion দেয়
* Migration Manage করে
* Multiple Database Support করে
* Developer Productivity বৃদ্ধি করে

---

# 📚 Next Part

পরবর্তী পর্বে আমরা শিখবো:

* Prisma Installation
* Prisma Setup
* PostgreSQL Connection
* Environment Variables
* First Migration
* Prisma Studio

---

Happy Coding! 🚀
