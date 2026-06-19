# 🚀 Prisma Part 2: Installing Prisma & Setting Up PostgreSQL (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের দ্বিতীয় পর্ব।

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Prisma Install করা
* PostgreSQL Setup করা
* Prisma Initialize করা
* Environment Variables Configure করা
* Database Connect করা
* First Migration Run করা
* Prisma Studio ব্যবহার করা

---

# 📋 Prerequisites

Prisma Setup করার আগে নিশ্চিত হও যে নিচের Tools গুলো Install করা আছে।

## Node.js

Version Check:

```bash
node -v
```

Example Output:

```bash
v22.0.0
```

## npm

Version Check:

```bash
npm -v
```

---

# 🗄️ PostgreSQL Setup

Prisma বিভিন্ন Database Support করে:

* PostgreSQL
* MySQL
* SQLite
* SQL Server
* MongoDB

এই Tutorial-এ আমরা PostgreSQL ব্যবহার করবো।

---

## Create Database

PostgreSQL Install করার পর নতুন Database তৈরি করো:

```sql
CREATE DATABASE prisma_learning;
```

Database List দেখার জন্য:

```sql
\l
```

---

# 📁 Create New Project

নতুন Project Folder তৈরি করো:

```bash
mkdir prisma-learning
```

Project Folder-এ প্রবেশ করো:

```bash
cd prisma-learning
```

---

# 📦 Initialize Node Project

```bash
npm init -y
```

এটি একটি `package.json` File তৈরি করবে।

---

# 📥 Install Prisma

## Prisma CLI Install

```bash
npm install prisma --save-dev
```

---

## Prisma Client Install

```bash
npm install @prisma/client
```

---

# 🚀 Initialize Prisma

নিচের Command Run করো:

```bash
npx prisma init
```

---

## Generated Project Structure

```text
prisma-learning/
│
├── prisma/
│   └── schema.prisma
│
├── .env
│
├── package.json
│
└── node_modules/
```

---

# 📄 Understanding schema.prisma

Prisma Init করার পর নিচের File তৈরি হবে:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

---

## Generator Block

```prisma
generator client {
  provider = "prisma-client-js"
}
```

এই Block Prisma Client Generate করে।

---

## Datasource Block

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

এই Block Database Connection Configure করে।

---

# 🔐 Environment Variables

Prisma Database Credentials `.env` File-এ সংরক্ষণ করে।

Default:

```env
DATABASE_URL=""
```

---

## PostgreSQL Connection String

Format:

```env
DATABASE_URL="postgresql://USERNAME:PASSWORD@localhost:5432/DATABASE_NAME"
```

Example:

```env
DATABASE_URL="postgresql://postgres:123456@localhost:5432/prisma_learning"
```

---

# 🏗️ Create First Model

`schema.prisma` File-এ নিচের Model যোগ করো:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

---

## Model Explanation

### Primary Key

```prisma
id Int @id @default(autoincrement())
```

* Integer Type
* Primary Key
* Auto Increment

### Unique Field

```prisma
email String @unique
```

একই Email একাধিকবার ব্যবহার করা যাবে না।

---

# 🔄 First Migration

Database Table তৈরি করার জন্য নিচের Command Run করো:

```bash
npx prisma migrate dev --name init
```

---

## Migration Flow

```text
Schema
  ↓
Migration
  ↓
Database Table
```

Prisma স্বয়ংক্রিয়ভাবে:

* Migration File তৈরি করবে
* Database Update করবে
* Prisma Client Generate করবে

---

# 📂 Migration Structure

Migration Run করার পর:

```text
prisma/
│
├── migrations/
│   └── 2026xxxx_init/
│       └── migration.sql
│
└── schema.prisma
```

---

# ⚡ Generate Prisma Client

Prisma Client Generate করার জন্য:

```bash
npx prisma generate
```

---

# 📖 Using Prisma Client

নতুন File তৈরি করো:

```js
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();
```

---

# ➕ Create First User

```js
const user = await prisma.user.create({
  data: {
    name: "Yeasin",
    email: "yeasin@gmail.com"
  }
});

console.log(user);
```

---

# 📄 Fetch All Users

```js
const users = await prisma.user.findMany();

console.log(users);
```

---

# 🖥️ Prisma Studio

Prisma Studio হলো Prisma-এর Built-in Database GUI Tool।

Run:

```bash
npx prisma studio
```

---

## Studio URL

```text
http://localhost:5555
```

---

## Studio Features

* View Records
* Insert Records
* Update Records
* Delete Records
* Search Records

---

# 🔄 Complete Setup Workflow

```text
Install PostgreSQL
         │
         ▼
Create Database
         │
         ▼
npm init -y
         │
         ▼
Install Prisma
         │
         ▼
npx prisma init
         │
         ▼
Configure .env
         │
         ▼
Create Models
         │
         ▼
npx prisma migrate dev
         │
         ▼
npx prisma generate
         │
         ▼
Use Prisma Client
         │
         ▼
Open Prisma Studio
```

---

# ⚠️ Common Beginner Mistakes

## Wrong Database URL

```env
DATABASE_URL="postgresql://wrong-user:pass@localhost:5432/db"
```

Database Connection Failed হবে।

---

## Forgetting Migration

Model পরিবর্তন করার পর Migration Run করতে হবে।

```bash
npx prisma migrate dev
```

---

## Forgetting Prisma Generate

নতুন Model Add করার পর:

```bash
npx prisma generate
```

Run করতে ভুলবে না।

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

✅ PostgreSQL Setup

✅ Prisma Install

✅ Prisma Initialize

✅ Environment Variables Configure

✅ Database Connect

✅ Create First Model

✅ Run Migration

✅ Generate Prisma Client

✅ Launch Prisma Studio

---

# 📚 Next Part

### Prisma Schema Deep Dive

পরবর্তী পর্বে আমরা বিস্তারিত শিখবো:

* Prisma Schema
* Generator
* Datasource
* Models
* Fields
* Attributes
* Scalar Types
* Best Practices

Prisma-এর সবচেয়ে গুরুত্বপূর্ণ Concept এখান থেকেই শুরু হবে। 🚀
