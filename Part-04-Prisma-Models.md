# 🚀 Prisma Part 4: Prisma Models Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের চতুর্থ পর্ব।

---

# 📖 Prisma Model কী?

Prisma-তে **Model** হলো Database Table-এর Representation।

প্রতিটি Model Database-এর একটি Table তৈরি করে এবং প্রতিটি Field Table-এর একটি Column তৈরি করে।

Example:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String
}
```

Equivalent SQL:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  email TEXT NOT NULL
);
```

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Model কী
* Model Naming Convention
* Primary Keys
* UUID
* Unique Constraints
* Composite Keys
* Indexes
* Enum
* Database Mapping
* Real World Model Design

---

# 🏗️ Model Naming Convention

Prisma Official Best Practice অনুযায়ী Model Name সবসময় Singular এবং PascalCase হওয়া উচিত।

## ✅ Good Examples

```prisma
model User
```

```prisma
model Product
```

```prisma
model Order
```

```prisma
model BlogPost
```

---

## ❌ Bad Examples

```prisma
model Users
```

```prisma
model products
```

```prisma
model user_table
```

---

# 🔑 Primary Keys

Primary Key প্রতিটি Record-কে Uniquely Identify করে।

---

## Auto Increment Primary Key

সবচেয়ে জনপ্রিয় Approach:

```prisma
model User {
  id Int @id @default(autoincrement())
}
```

Generated SQL:

```sql
id SERIAL PRIMARY KEY
```

---

# 🆔 UUID Primary Key

Production Application-এ UUID ব্যাপকভাবে ব্যবহৃত হয়।

```prisma
model User {
  id String @id @default(uuid())
}
```

Example UUID:

```text
550e8400-e29b-41d4-a716-446655440000
```

---

# ⚖️ Auto Increment vs UUID

| Feature             | Auto Increment | UUID |
| ------------------- | -------------- | ---- |
| Easy to Read        | ✅              | ❌    |
| Predictable         | ❌              | ✅    |
| Distributed Systems | ❌              | ✅    |
| Security            | ❌              | ✅    |
| Storage Efficient   | ✅              | ❌    |

---

# 🔒 Unique Constraints

একটি Field-এর Value Unique রাখতে `@unique` ব্যবহার করা হয়।

## Example

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
}
```

---

## Allowed Values

```text
john@gmail.com
rahim@gmail.com
```

---

## Not Allowed

```text
john@gmail.com
john@gmail.com
```

---

# 🔥 Multiple Unique Fields

একাধিক Field-এ Unique Constraint ব্যবহার করা যায়।

```prisma
model User {
  id       Int    @id @default(autoincrement())
  email    String @unique
  username String @unique
}
```

---

# 🧩 Composite Unique Constraint

দুই বা তার বেশি Field একসাথে Unique করতে `@@unique` ব্যবহার করা হয়।

## Example

```prisma
model Enrollment {
  studentId Int
  courseId  Int

  @@unique([studentId, courseId])
}
```

---

## Allowed Data

| studentId | courseId |
| --------- | -------- |
| 1         | 101      |
| 1         | 102      |

---

## Not Allowed

| studentId | courseId |
| --------- | -------- |
| 1         | 101      |
| 1         | 101      |

---

# 🏗️ Composite Primary Key

একাধিক Field মিলে Primary Key তৈরি করা যায়।

```prisma
model Enrollment {
  studentId Int
  courseId  Int

  @@id([studentId, courseId])
}
```

Equivalent SQL:

```sql
PRIMARY KEY(studentId, courseId)
```

---

# ⚡ Indexes

Index Database Query Performance Improve করে।

---

## Single Column Index

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String

  @@index([email])
}
```

---

## Multiple Column Index

```prisma
model Order {
  id         Int      @id @default(autoincrement())
  customerId Int
  status     String

  @@index([customerId, status])
}
```

---

Useful Query:

```sql
SELECT *
FROM orders
WHERE customer_id = 1
AND status = 'pending';
```

---

# 🎨 Enum

Enum ব্যবহার করে নির্দিষ্ট কিছু Value Restrict করা যায়।

---

## Create Enum

```prisma
enum UserRole {
  ADMIN
  MANAGER
  EMPLOYEE
}
```

---

## Use Enum in Model

```prisma
model User {
  id   Int      @id @default(autoincrement())
  name String
  role UserRole
}
```

---

## Valid Values

```text
ADMIN
MANAGER
EMPLOYEE
```

---

## Invalid Values

```text
SUPER_ADMIN
OWNER
```

---

# 🗺️ Database Mapping

Prisma Model Name এবং Database Table Name আলাদা হতে পারে।

---

## Table Mapping

```prisma
model User {
  id Int @id @default(autoincrement())

  @@map("users")
}
```

---

### Prisma Side

```text
User
```

---

### Database Side

```text
users
```

---

# 📄 Field Mapping

```prisma
model User {
  id Int @id @default(autoincrement())

  firstName String @map("first_name")
}
```

---

### Prisma

```js
user.firstName
```

---

### Database

```sql
first_name
```

---

# 🏢 Real World User Model

```prisma
enum UserRole {
  ADMIN
  CUSTOMER
}

model User {
  id        String   @id @default(uuid())
  name      String
  email     String   @unique
  role      UserRole @default(CUSTOMER)

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([email])
}
```

---

# 🛒 Product Model

```prisma
model Product {
  id          Int      @id @default(autoincrement())
  title       String
  description String?
  price       Float

  createdAt   DateTime @default(now())

  @@index([title])
}
```

---

# 🧠 Best Practices

## Use UUID in Production Applications

```prisma
id String @id @default(uuid())
```

---

## Use Unique Email

```prisma
email String @unique
```

---

## Add Timestamps

```prisma
createdAt DateTime @default(now())
updatedAt DateTime @updatedAt
```

---

## Prefer Enum Over String

❌ Bad

```prisma
role String
```

✅ Good

```prisma
role UserRole
```

---

## Add Indexes on Frequently Queried Fields

```prisma
@@index([email])
```

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

✅ Model Design

✅ Naming Convention

✅ Primary Keys

* Auto Increment
* UUID

✅ Unique Constraints

✅ Composite Unique Constraints

✅ Composite Primary Keys

✅ Indexes

✅ Enum

✅ Database Mapping

✅ Real World Model Design

---

# 📚 Next Part

## Prisma Relations Deep Dive

পরবর্তী পর্বে আমরা শিখবো:

* One-to-One Relationship
* One-to-Many Relationship
* Many-to-Many Relationship
* Foreign Keys
* Relation Queries
* Nested Writes
* Cascade Delete

Prisma-এর সবচেয়ে গুরুত্বপূর্ণ এবং বাস্তব প্রজেক্টে সবচেয়ে বেশি ব্যবহৃত বিষয় এখান থেকেই শুরু হবে। 🔥
