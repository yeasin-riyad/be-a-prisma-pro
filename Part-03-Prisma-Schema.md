# 🚀 Prisma Part 3: Prisma Schema Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের তৃতীয় পর্ব।

---

# 📖 Prisma Schema কী?

Prisma Schema হলো Prisma Project-এর Blueprint বা নকশা।

এখানে Define করা হয়:

* Database Connection
* Database Models
* Relationships
* Prisma Client Configuration

Prisma Project-এর সবচেয়ে গুরুত্বপূর্ণ File হলো:

```text
prisma/
└── schema.prisma
```

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Prisma Schema Structure
* Generator Block
* Datasource Block
* Models
* Fields
* Scalar Types
* Attributes
* Optional Fields
* Schema Best Practices

---

# 🏗️ Basic Prisma Schema Structure

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

# 🔥 Prisma Schema Components

Prisma Schema মূলত তিনটি অংশ নিয়ে গঠিত:

```text
Schema
│
├── Generator
├── Datasource
└── Models
```

---

# ⚙️ Generator Block

Generator Block Prisma Client Generate করার জন্য ব্যবহার করা হয়।

```prisma
generator client {
  provider = "prisma-client-js"
}
```

## Explanation

### Generator Name

```prisma
generator client
```

এখানে `client` হলো Generator Name।

---

### Provider

```prisma
provider = "prisma-client-js"
```

এটি JavaScript/TypeScript-এর জন্য Prisma Client Generate করে।

---

# 🔗 Datasource Block

Datasource Database Connection Configure করে।

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

---

## Datasource Name

```prisma
datasource db
```

এখানে `db` হলো Datasource Name।

---

## Database Provider

```prisma
provider = "postgresql"
```

Supported Database Providers:

```text
postgresql
mysql
sqlite
sqlserver
mongodb
cockroachdb
```

---

## URL Configuration

```prisma
url = env("DATABASE_URL")
```

Database Connection String `.env` File থেকে Load করা হয়।

Example:

```env
DATABASE_URL="postgresql://postgres:123456@localhost:5432/prisma_learning"
```

---

# 🏗️ Models

Prisma-তে Model একটি Database Table-এর Representation।

Example:

```prisma
model User {
  id    Int
  name  String
  email String
}
```

Equivalent SQL:

```sql
CREATE TABLE User (
  id INT,
  name TEXT,
  email TEXT
);
```

---

# 📄 Fields

Model-এর প্রতিটি Property-কে Field বলা হয়।

Example:

```prisma
model User {
  id    Int
  name  String
  email String
}
```

Fields:

```text
id
name
email
```

---

# 🔤 Prisma Scalar Types

Prisma কিছু Built-in Data Types প্রদান করে।

---

## String

```prisma
name String
```

Example:

```text
Yeasin
Riyad
John
```

---

## Int

```prisma
age Int
```

Example:

```text
20
30
100
```

---

## Float

```prisma
price Float
```

Example:

```text
99.99
150.50
```

---

## Boolean

```prisma
isActive Boolean
```

Values:

```text
true
false
```

---

## DateTime

```prisma
createdAt DateTime
```

Example:

```text
2026-06-19T10:30:00Z
```

---

## Json

```prisma
settings Json
```

Example:

```json
{
  "theme": "dark"
}
```

---

## Bytes

```prisma
file Bytes
```

Binary Data Store করার জন্য ব্যবহৃত হয়।

---

# 🏷️ Field Attributes

Attributes Field-এর Behaviour Control করে।

---

# @id

Primary Key Define করে।

```prisma
id Int @id
```

Equivalent SQL:

```sql
PRIMARY KEY
```

---

# @default

Default Value Set করে।

Example:

```prisma
id Int @default(autoincrement())
```

---

## Current Time Default

```prisma
createdAt DateTime @default(now())
```

Record Create হওয়ার সময় Current Timestamp Save হবে।

---

# @unique

Unique Constraint তৈরি করে।

```prisma
email String @unique
```

Allowed:

```text
john@gmail.com
rahim@gmail.com
```

Not Allowed:

```text
john@gmail.com
john@gmail.com
```

---

# @updatedAt

Record Update হওয়ার সময় Automatically Timestamp Update হয়।

```prisma
updatedAt DateTime @updatedAt
```

---

# ✅ Complete Example

```prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

---

# ❓ Optional Fields

Defaultভাবে Prisma-এর সব Fields Required।

---

## Required Field

```prisma
name String
```

---

## Optional Field

```prisma
phone String?
```

Question Mark (`?`) Field-কে Optional করে।

---

## Example

```prisma
model User {
  id    Int     @id @default(autoincrement())
  name  String
  phone String?
}
```

Valid Data:

```json
{
  "name": "Yeasin"
}
```

---

# 📚 Real World User Model

```prisma
model User {
  id          Int      @id @default(autoincrement())
  name        String
  email       String   @unique
  phone       String?
  age         Int?
  profilePic  String?
  isActive    Boolean  @default(true)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

---

# 🧠 Schema Best Practices

## Use Singular Model Names

✅ Good

```prisma
model User
```

❌ Bad

```prisma
model Users
```

---

## Always Use Primary Key

```prisma
id Int @id @default(autoincrement())
```

---

## Add createdAt

```prisma
createdAt DateTime @default(now())
```

---

## Add updatedAt

```prisma
updatedAt DateTime @updatedAt
```

---

## Use Unique Constraints

```prisma
email String @unique
```

---

# 🔄 Migration After Schema Changes

Schema Update করার পর Migration Run করতে হবে।

```bash
npx prisma migrate dev --name update_user_model
```

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

✅ Prisma Schema

✅ Generator Block

✅ Datasource Block

✅ Models

✅ Fields

✅ Scalar Types

✅ Attributes

* @id
* @default
* @unique
* @updatedAt

✅ Optional Fields

✅ Schema Best Practices

---

# 📚 Next Part

## Prisma Models Deep Dive

পরবর্তী পর্বে আমরা শিখবো:

* Model Naming Convention
* Primary Keys
* Composite Keys
* Unique Constraints
* Indexes
* Enum
* Database Mapping
* Advanced Data Modeling

Prisma Data Modeling-এর Core Foundation শুরু হবে এই অধ্যায় থেকে। 🚀
