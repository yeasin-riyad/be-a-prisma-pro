# 🚀 Prisma Part 7: Migrations Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের সপ্তম পর্ব।

---

# 📖 Prisma Migration কী?

Prisma Migration হলো **Database Version Control System**।

যেভাবে **Git** আমাদের Source Code-এর পরিবর্তনগুলো Track করে, ঠিক সেভাবেই Prisma Migration Database Schema-এর পরিবর্তনগুলো Track করে।

Migration-এর মাধ্যমে আমরা নিরাপদভাবে Database Structure পরিবর্তন করতে পারি এবং পুরো Team একই Schema ব্যবহার করতে পারে।

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Prisma Migration কী
* Migration Workflow
* `migrate dev`
* `migrate deploy`
* `migrate reset`
* `db push`
* `db pull`
* `prisma generate`
* Migration Files
* Shadow Database
* Migration History
* Team Workflow
* Production Best Practices

---

# 🔄 What is a Migration?

ধরো প্রথমে আমাদের User Model এমন ছিল:

```text
Version 1

User
---------
id
name
```

পরে আমরা Email যোগ করলাম:

```text
Version 2

User
---------
id
name
email
```

আবার Phone যোগ করলাম:

```text
Version 3

User
---------
id
name
email
phone
```

Prisma প্রতিটি পরিবর্তনকে একটি Migration File হিসেবে সংরক্ষণ করে।

---

# 🏗️ Migration Workflow

```text
schema.prisma

      │

      ▼

npx prisma migrate dev

      │

      ▼

Migration File

      │

      ▼

Database Update

      │

      ▼

Prisma Client Generate
```

---

# 📁 Migration Folder Structure

```text
prisma/

├── migrations/
│
├── 20260623094512_init/
│   └── migration.sql
│
├── 20260623101530_add_email/
│   └── migration.sql
│
└── schema.prisma
```

---

# 🛠️ migrate dev

Development Environment-এর জন্য সবচেয়ে বেশি ব্যবহৃত Command।

```bash
npx prisma migrate dev
```

---

## Example

Schema:

```prisma
model User {
  id   Int    @id @default(autoincrement())
  name String
}
```

Migration Run:

```bash
npx prisma migrate dev --name init
```

---

## Prisma কী করবে?

* ✅ Migration File তৈরি করবে
* ✅ Database Update করবে
* ✅ Prisma Client Generate করবে
* ✅ Migration History Save করবে

---

# 📝 Migration Naming

Meaningful নাম ব্যবহার করা উচিত।

## ✅ Good

```bash
npx prisma migrate dev --name init

npx prisma migrate dev --name add_user_table

npx prisma migrate dev --name add_profile_model

npx prisma migrate dev --name add_product_price
```

---

## ❌ Bad

```bash
test

abc

migration1

new
```

---

# 📄 Generated SQL

Prisma Migration File-এর ভিতরে SQL থাকে।

```sql
CREATE TABLE "User" (

  "id" SERIAL PRIMARY KEY,

  "name" TEXT NOT NULL

);
```

---

# ➕

## Example: Add Email Field

Before:

```prisma
model User {
  id   Int    @id @default(autoincrement())
  name String
}
```

After:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

Run:

```bash
npx prisma migrate dev --name add_email
```

Generated SQL:

```sql
ALTER TABLE "User"

ADD COLUMN "email" TEXT;

CREATE UNIQUE INDEX;
```

---

# 🚀 migrate deploy

Production Server-এর জন্য ব্যবহৃত হয়।

```bash
npx prisma migrate deploy
```

---

## Workflow

```text
Existing Migration Files

        │

        ▼

Apply to Production Database

        │

        ▼

No New Migration Created
```

---

# 🔥 migrate reset

Development Database সম্পূর্ণ Reset করে।

```bash
npx prisma migrate reset
```

---

## এটি কী করবে?

```text
Drop Database

      │

      ▼

Run All Migrations Again

      │

      ▼

Generate Prisma Client

      │

      ▼

Run Seed (if exists)
```

---

⚠️ **Production Database-এ কখনো ব্যবহার করা উচিত নয়।**

---

# ⚡ db push

Migration File তৈরি না করেই Database Update করে।

```bash
npx prisma db push
```

---

## Workflow

```text
schema.prisma

      │

      ▼

db push

      │

      ▼

Database Updated

(No Migration History)
```

---

## Use Cases

* Learning
* Prototype
* Quick Testing

---

## Avoid For

* Team Collaboration
* Production Projects
* Version Controlled Database

---

# 📥 db pull

Existing Database থেকে Prisma Schema Generate করে।

```bash
npx prisma db pull
```

---

## Workflow

```text
Existing Database

      │

      ▼

db pull

      │

      ▼

schema.prisma Updated
```

---

## Useful For

* Legacy Database
* Existing Project
* Reverse Engineering

---

# ⚙️ prisma generate

Prisma Client Generate করার জন্য।

```bash
npx prisma generate
```

---

## কখন Run করতে হবে?

```text
Schema Change

      │

      ▼

Generate Client

      │

      ▼

Use New API
```

---

# 🌑 Shadow Database

Prisma Migration Validate করার জন্য Temporary Database ব্যবহার করে।

```text
Main Database

      │

      ▼

Shadow Database

      │

      ▼

Compare Schema

      │

      ▼

Validate Migration

      │

      ▼

Apply Migration
```

---

## Benefits

* Safe Migration
* Schema Validation
* Consistent Migration

---

# 📜 Migration History

Prisma Database-এর মধ্যে একটি Table তৈরি করে।

```text
_prisma_migrations
```

---

এখানে Store হয়:

* Migration Name
* Applied Time
* Checksum
* Status

---

# 🏢 Real World Workflow

## Day 1

```text
Create User Model

↓

migrate dev --name init
```

---

## Day 2

```text
Add Email

↓

migrate dev --name add_email
```

---

## Day 3

```text
Add Profile Model

↓

migrate dev --name add_profile
```

---

Migration History:

```text
init

↓

add_email

↓

add_profile
```

---

# 👨‍💻 Team Workflow

## Developer A

```text
git pull

↓

npx prisma migrate dev
```

---

## Developer B

```text
Create Migration

↓

git push
```

---

## Production Server

```text
git pull

↓

npx prisma migrate deploy
```

---

# ⚖️ db push vs migrate dev

| db push            | migrate dev        |
| ------------------ | ------------------ |
| No Migration File  | Creates Migration  |
| Prototype          | Development        |
| No Version History | Version Controlled |
| Quick Changes      | Recommended        |

---

# ⚖️ migrate dev vs migrate deploy

| migrate dev       | migrate deploy             |
| ----------------- | -------------------------- |
| Development       | Production                 |
| Creates Migration | Applies Existing Migration |
| Generates Client  | No Client Generation       |
| Local Development | Server Deployment          |

---

# 🧠 Best Practices

## ✅ Use Meaningful Migration Names

```text
add_user_table

add_profile_model

add_order_status

add_product_category
```

---

## ✅ Development

```bash
npx prisma migrate dev
```

---

## ✅ Production

```bash
npx prisma migrate deploy
```

---

## ✅ Prototype / Learning

```bash
npx prisma db push
```

---

## ✅ Commit Migration Files

```bash
git add prisma/migrations
git commit -m "add user migration"
```

---

## ❌ Never Delete Migration Folder

Migration Folder Delete করলে পুরো Team-এর Database History নষ্ট হয়ে যেতে পারে।

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

* ✅ Prisma Migration
* ✅ migrate dev
* ✅ migrate deploy
* ✅ migrate reset
* ✅ db push
* ✅ db pull
* ✅ prisma generate
* ✅ Migration Files
* ✅ Shadow Database
* ✅ Migration History
* ✅ Team Workflow
* ✅ Production Best Practices

---

# 📚 Next Part

## Prisma Seeding & Initial Data

পরবর্তী অধ্যায়ে আমরা শিখবো:

* Prisma Seed কী
* Seed Script তৈরি করা
* Initial Data Insert করা
* Admin User Create করা
* Faker ব্যবহার করে Fake Data Generate করা
* Reset + Seed Workflow
* Production Seeding Strategy
* Seeding Best Practices

এটি বাস্তব Project Development-এর জন্য অত্যন্ত গুরুত্বপূর্ণ একটি অধ্যায়। 🚀
