# 🚀 Prisma পরিচিতি (Introduction to Prisma)

## 📌 Prisma কী?

**Prisma** হলো Node.js এবং TypeScript-এর জন্য একটি **Modern ORM (Object Relational Mapping)** টুল, যা ডেভেলপারদের সহজে Database-এর সাথে কাজ করতে সাহায্য করে।

সহজ ভাষায়, Prisma ব্যবহার করে SQL query না লিখেও JavaScript/TypeScript কোডের মাধ্যমে Database পরিচালনা করা যায়।

Prisma বিভিন্ন Database সাপোর্ট করে, যেমন:

* PostgreSQL
* MySQL
* SQLite
* SQL Server
* MongoDB

---

# 🧠 ORM কী?

ORM (Object Relational Mapping) এমন একটি প্রযুক্তি যা Database Table-কে Programming Object-এর সাথে ম্যাপ করে।

উদাহরণ:

### Raw SQL

```sql
SELECT * FROM users WHERE id = 1;
```

### Prisma

```javascript
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
});
```

এখানে SQL না লিখেই JavaScript কোড দিয়ে ডেটা আনা হচ্ছে।

---

# ✨ Prisma কেন ব্যবহার করবেন?

✅ Type Safety

✅ Auto Completion (IntelliSense)

✅ Migration Support

✅ Developer Friendly

✅ সহজ Query লেখা

✅ SQL Injection ঝুঁকি কম

---

# 🏗️ Prisma Architecture

Prisma-এর প্রধান ৩টি অংশ রয়েছে:

## ১. Prisma Schema

`schema.prisma` ফাইলে Database configuration এবং Model লেখা হয়।

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

---

## ২. Prisma Client

Prisma Client একটি Auto-generated Query Builder।

উদাহরণ:

```javascript
const users = await prisma.user.findMany();
```

---

## ৩. Prisma Migrate

Database Schema পরিবর্তনের জন্য Migration ব্যবহার করা হয়।

```bash
npx prisma migrate dev --name init
```

---

# ⚙️ Installation

## Step 1: Project Initialize

```bash
npm init -y
```

## Step 2: Prisma Install

```bash
npm install prisma --save-dev
npm install @prisma/client
```

## Step 3: Prisma Initialize

```bash
npx prisma init
```

এতে নিচের ফাইলগুলো তৈরি হবে:

```text
prisma/
    schema.prisma

.env
```

---

# 🔗 Database Connection

### PostgreSQL

```env
DATABASE_URL="postgresql://username:password@localhost:5432/mydb"
```

### MySQL

```env
DATABASE_URL="mysql://username:password@localhost:3306/mydb"
```

---

# 📦 Model তৈরি

```prisma
model Student {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
  age   Int
}
```

Migration চালান:

```bash
npx prisma migrate dev --name create_student
```

---

# 🔨 Prisma Client Generate

```bash
npx prisma generate
```

---

# 📝 CRUD Operation

## ➕ Create

```javascript
await prisma.student.create({
  data: {
    name: "Yeasin",
    email: "yeasin@gmail.com",
    age: 24,
  },
});
```

---

## 📖 Read

```javascript
const students = await prisma.student.findMany();
```

---

## ✏️ Update

```javascript
await prisma.student.update({
  where: {
    email: "yeasin@gmail.com",
  },
  data: {
    age: 25,
  },
});
```

---

## ❌ Delete

```javascript
await prisma.student.delete({
  where: {
    email: "yeasin@gmail.com",
  },
});
```

---

# 🔄 Prisma Relation (One-to-Many)

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String

  posts Post[]
}

model Post {
  id      Int   @id @default(autoincrement())
  title   String

  userId  Int
  user    User @relation(fields: [userId], references: [id])
}
```

এখানে একজন User-এর একাধিক Post থাকতে পারে।

---

# 🎨 Prisma Studio

Database GUI চালানোর জন্য:

```bash
npx prisma studio
```

এর মাধ্যমে Browser থেকেই Database দেখতে এবং Edit করতে পারবেন।

---

# 📊 Prisma-এর সুবিধা

| বৈশিষ্ট্য       | ব্যাখ্যা                          |
| --------------- | --------------------------------- |
| Type Safety     | Compile time-এ Error ধরা যায়     |
| Auto Completion | VS Code Suggestion দেয়           |
| Migration       | Database Version Control করা যায় |
| Security        | SQL Injection ঝুঁকি কম            |
| Easy Query      | SQL কম লিখতে হয়                  |

---

# ⚔️ Prisma vs Raw SQL

| Prisma              | Raw SQL        |
| ------------------- | -------------- |
| সহজ Syntax          | তুলনামূলক কঠিন |
| Type Safe           | Type Safe নয়  |
| Auto Completion আছে | নেই            |
| Maintain করা সহজ    | জটিল হতে পারে  |

---

# 🎤 Viva Tip

উত্তর শুরু করতে পারেন এভাবে:

> **"From my understanding, Prisma is a modern ORM for Node.js and TypeScript that enables developers to interact with databases using JavaScript instead of writing raw SQL queries."**

অথবা,

> **"In modern web development, Prisma simplifies database operations through type-safe queries, migrations, and an auto-generated client."**

এভাবে বললে আপনার উত্তর আরও professional এবং confident শোনাবে।

---

## 📚 উপসংহার

Prisma একটি শক্তিশালী ORM যা Database-এর সাথে কাজকে অনেক সহজ, নিরাপদ এবং দ্রুত করে তোলে। বিশেষ করে Node.js এবং TypeScript ডেভেলপারদের জন্য Prisma বর্তমানে সবচেয়ে জনপ্রিয় ORM-গুলোর একটি।
