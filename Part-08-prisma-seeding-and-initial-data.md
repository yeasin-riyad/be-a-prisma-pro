# 🌱 Prisma Part 8: Seeding & Initial Data (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের অষ্টম পর্ব।

---

# 📖 Prisma Seeding কী?

Database Migration করার পরে Database-এর Structure তৈরি হয়, কিন্তু সেখানে কোনো Data থাকে না।

Default Admin, Roles, Categories, Settings অথবা Fake Test Data Automatically Insert করার Process-কে **Prisma Seeding** বলা হয়।

সহজভাবে,

> **Seeding = Database-এ Initial Data Insert করার Automated Process**

---

# 🎯 Learning Goals

এই অধ্যায়ে আমরা শিখবো:

* Prisma Seed কী
* কেন Seed ব্যবহার করা হয়
* Seed Script Configure করা
* Initial Admin User তৈরি করা
* Multiple Records Insert করা
* Faker দিয়ে Fake Data Generate করা
* Reset + Seed Workflow
* Environment Based Seeding
* Performance Tips
* Best Practices

---

# 🌍 কেন Seeding ব্যবহার করা হয়?

ধরো তুমি একটি E-commerce Project তৈরি করছো।

Migration Run করার পরে Database হবে:

```text
Users

Products

Categories

Roles

Permissions

↓

Empty Database
```

Seed Run করার পরে:

```text
Users
├── Super Admin
├── Manager

Categories
├── Electronics
├── Books
├── Fashion

Roles
├── ADMIN
├── USER

Products
├── Laptop
├── Mouse
├── Keyboard
```

সব Initial Data Automatically Insert হয়ে যাবে।

---

# 📁 Folder Structure

```text
project/

├── prisma/
│   ├── schema.prisma
│   ├── seed.ts
│   └── migrations/
│
├── package.json
└── src/
```

---

# ⚙️ Configure package.json

```json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

---

# 📦 Install Required Packages

```bash
npm install tsx

npm install @faker-js/faker
```

---

# 🚀 Basic Seed Script

```typescript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

async function main() {

  await prisma.user.create({

    data: {

      name: "Admin",

      email: "admin@gmail.com"

    }

  });

}

main()
  .finally(async () => {

    await prisma.$disconnect();

  });
```

---

# ▶️ Run Seed

```bash
npx prisma db seed
```

Output:

```text
Running seed command...

Seed completed successfully.
```

---

# 👤 Create Initial Admin User

Schema:

```prisma
model User {

  id Int @id @default(autoincrement())

  name String

  email String @unique

  role String

}
```

Seed:

```typescript
await prisma.user.create({

  data: {

    name: "Super Admin",

    email: "admin@gmail.com",

    role: "ADMIN"

  }

});
```

---

# 📚 Insert Multiple Records

```typescript
await prisma.category.createMany({

  data: [

    {
      name: "Electronics"
    },

    {
      name: "Books"
    },

    {
      name: "Fashion"
    }

  ]

});
```

---

# 🔥 Skip Duplicate Records

```typescript
await prisma.category.createMany({

  data: categories,

  skipDuplicates: true

});
```

---

# 🎲 Faker Integration

Fake Data Generate করার জন্য জনপ্রিয় Package:

```text
@faker-js/faker
```

Install:

```bash
npm install @faker-js/faker
```

---

# 👨‍💻 Generate Fake User

```typescript
import { faker } from "@faker-js/faker";

await prisma.user.create({

  data: {

    name: faker.person.fullName(),

    email: faker.internet.email()

  }

});
```

---

# 🔄 Generate 100 Fake Users

```typescript
import { faker } from "@faker-js/faker";

const users = [];

for (let i = 0; i < 100; i++) {

  users.push({

    name: faker.person.fullName(),

    email: faker.internet.email()

  });

}

await prisma.user.createMany({

  data: users

});
```

---

# 🏢 Real Project Seed Structure

```text
Seed

├── Roles

├── Admin User

├── Categories

├── Products

├── Settings

└── Permissions
```

---

# 📝 Modular Seed Functions

```typescript
async function seedRoles() {

}

async function seedCategories() {

}

async function seedProducts() {

}

async function seedAdmin() {

}
```

Main Function:

```typescript
async function main() {

  await seedRoles();

  await seedCategories();

  await seedProducts();

  await seedAdmin();

}
```

---

# 🔒 Idempotent Seed

একই Seed বারবার Run করলে Duplicate Data তৈরি হওয়া উচিত নয়।

---

## ❌ Bad Practice

```typescript
await prisma.user.create({

  data: {

    email: "admin@gmail.com"

  }

});
```

---

## ✅ Good Practice

```typescript
await prisma.user.upsert({

  where: {

    email: "admin@gmail.com"

  },

  update: {},

  create: {

    name: "Admin",

    email: "admin@gmail.com"

  }

});
```

---

# 🔄 Reset + Seed Workflow

Command:

```bash
npx prisma migrate reset
```

Workflow:

```text
Drop Database

↓

Run All Migrations

↓

Generate Prisma Client

↓

Run Seed Script

↓

Database Ready
```

---

# 🌎 Environment Based Seeding

Development:

```typescript
if (process.env.NODE_ENV === "development") {

  // Fake Data

}
```

Production:

```typescript
if (process.env.NODE_ENV === "production") {

  // Admin + Roles + Settings

}
```

---

# 📊 Large Dataset Seeding

Example:

* 1000 Products
* 500 Users
* 100 Categories
* 20 Roles

সবসময় ব্যবহার করো:

```typescript
createMany()
```

Avoid:

```typescript
for (const user of users) {

  await prisma.user.create({

    data: user

  });

}
```

---

# ⚡ Performance Tips

## ✅ Fast

```typescript
await prisma.product.createMany({

  data: products

});
```

---

## ❌ Slow

```typescript
for (const product of products) {

  await prisma.product.create({

    data: product

  });

}
```

---

# 🧠 Best Practices

## ✅ Use Upsert for Default Data

```typescript
upsert()
```

---

## ✅ Use createMany() for Bulk Insert

```typescript
createMany()
```

---

## ✅ Keep Seed Modular

```text
seed/

├── roles.ts

├── users.ts

├── categories.ts

├── products.ts
```

---

## ✅ Separate Development & Production Seed

Development

```text
Fake Data
```

Production

```text
Real Initial Data
```

---

## ✅ Hash Password Before Insert

❌ Bad

```text
password: "12345678"
```

✅ Good

```typescript
bcrypt.hash(password)
```

---

# 🏗️ Complete Seed Flow

```text
Migration

↓

Generate Prisma Client

↓

Run Seed

↓

Create Admin

↓

Create Roles

↓

Create Categories

↓

Create Products

↓

Application Ready
```

---

# 📌 Summary

এই অধ্যায়ে আমরা শিখলাম:

* ✅ Prisma Seed
* ✅ Seed Configuration
* ✅ Seed Script
* ✅ Initial Admin User
* ✅ createMany()
* ✅ Upsert()
* ✅ Faker Integration
* ✅ Reset + Seed
* ✅ Environment Based Seeding
* ✅ Performance Optimization
* ✅ Best Practices

---

# 📚 Next Part

# Prisma Client Advanced Queries Deep Dive

পরবর্তী অধ্যায়ে আমরা শিখবো:

* Advanced Filtering
* Relation Filters
* Nested Queries
* Aggregation
* GroupBy
* Raw SQL
* Dynamic Query Building
* Query Optimization
* Performance Best Practices

এই অধ্যায় শেষ করার পর তুমি Production Level Prisma Query লিখতে পারবে এবং Complex Backend API তৈরি করতে সক্ষম হবে। 🚀
