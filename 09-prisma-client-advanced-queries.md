# 🚀 Prisma Part 9: Prisma Client Advanced Queries Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের নবম পর্ব।

---

# 📖 Introduction

এখন পর্যন্ত আমরা Prisma-এর Basic CRUD Operations শিখেছি। কিন্তু বাস্তব Project-এ শুধুমাত্র `findMany()` বা `findUnique()` জানলেই হয় না।

Production Application-এ আমাদের প্রয়োজন হয়—

* Advanced Filtering
* Dynamic Searching
* Relation Filtering
* Nested Queries
* Aggregation
* GroupBy
* Raw SQL
* Dynamic Query Building
* Query Optimization

এই অধ্যায়ে আমরা এগুলো বিস্তারিতভাবে শিখবো।

---

# 🎯 Learning Goals

এই অধ্যায়ে আমরা শিখবো:

* Advanced Filtering
* Logical Operators (`AND`, `OR`, `NOT`)
* String Filtering
* Relation Filtering
* Nested Select & Include
* Aggregate Queries
* GroupBy
* Distinct
* Offset Pagination
* Cursor Pagination
* Raw SQL
* Dynamic Query Building
* Query Optimization
* Performance Best Practices

---

# 📦 Sample Models

এই অধ্যায়ের সব উদাহরণ নিচের Model-এর উপর ভিত্তি করে।

```prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  age       Int
  isActive  Boolean  @default(true)

  posts     Post[]

  createdAt DateTime @default(now())
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  published Boolean  @default(false)

  userId    Int
  author    User @relation(fields: [userId], references: [id])
}
```

---

# 🔍 Advanced Filtering

Filtering ব্যবহার করে শুধুমাত্র প্রয়োজনীয় Data Database থেকে নিয়ে আসা যায়।

## Equal

```ts
await prisma.user.findMany({
  where: {
    age: 25
  }
});
```

---

## Not Equal

```ts
where: {
  age: {
    not: 25
  }
}
```

---

## Greater Than

```ts
where: {
  age: {
    gt: 18
  }
}
```

---

## Greater Than or Equal

```ts
where: {
  age: {
    gte: 18
  }
}
```

---

## Less Than

```ts
where: {
  age: {
    lt: 40
  }
}
```

---

## Less Than or Equal

```ts
where: {
  age: {
    lte: 30
  }
}
```

---

## Between

১৮ থেকে ৩০ বছরের User বের করা।

```ts
where: {
  age: {
    gte: 18,
    lte: 30
  }
}
```

---

# 🔤 String Filtering

## contains()

```ts
where: {
  name: {
    contains: "Yea"
  }
}
```

---

## startsWith()

```ts
where: {
  name: {
    startsWith: "Ye"
  }
}
```

---

## endsWith()

```ts
where: {
  name: {
    endsWith: "der"
  }
}
```

---

## Case Insensitive Search

```ts
where: {
  name: {
    contains: "ye",
    mode: "insensitive"
  }
}
```

---

# 🔗 Logical Operators

## AND

```ts
where: {
  AND: [
    {
      age: {
        gt: 18
      }
    },
    {
      isActive: true
    }
  ]
}
```

---

## OR

```ts
where: {
  OR: [
    {
      age: 20
    },
    {
      age: 25
    }
  ]
}
```

---

## NOT

```ts
where: {
  NOT: {
    isActive: false
  }
}
```

---

# 🔄 Relation Filtering

Published Post আছে এমন User খুঁজে বের করা।

```ts
await prisma.user.findMany({
  where: {
    posts: {
      some: {
        published: true
      }
    }
  }
});
```

---

## every

সব Post Published।

```ts
posts: {
  every: {
    published: true
  }
}
```

---

## none

কোন Published Post নেই।

```ts
posts: {
  none: {
    published: true
  }
}
```

---

# 📂 Nested Include

Relation সহ Data Fetch করা।

```ts
const users = await prisma.user.findMany({
  include: {
    posts: true
  }
});
```

Output:

```json
[
  {
    "name": "Yeasin",
    "posts": [
      {
        "title": "Prisma Tutorial"
      }
    ]
  }
]
```

---

# 🎯 Nested Select

শুধুমাত্র প্রয়োজনীয় Relation Field Fetch করা।

```ts
await prisma.user.findMany({
  select: {
    name: true,
    posts: {
      select: {
        title: true
      }
    }
  }
});
```

---

# 📊 Aggregate Queries

Average Age

```ts
await prisma.user.aggregate({
  _avg: {
    age: true
  }
});
```

---

Maximum

```ts
_max
```

---

Minimum

```ts
_min
```

---

Sum

```ts
_sum
```

---

Count

```ts
_count
```

---

# 📈 GroupBy

```ts
await prisma.user.groupBy({
  by: ["isActive"],
  _count: true
});
```

Output:

```text
Active Users   : 50

Inactive Users : 10
```

---

# 🎯 Distinct

Duplicate Value Remove করার জন্য।

```ts
await prisma.user.findMany({
  distinct: ["email"]
});
```

---

# 📄 Offset Pagination

```ts
await prisma.user.findMany({
  skip: 20,
  take: 10
});
```

---

# 🚀 Cursor Pagination

বড় Dataset-এর জন্য Recommended।

```ts
await prisma.user.findMany({
  take: 10,
  cursor: {
    id: 50
  }
});
```

---

# ⚡ Raw SQL

Prisma Query যথেষ্ট না হলে Raw SQL ব্যবহার করা যায়।

```ts
const users = await prisma.$queryRaw`

SELECT *

FROM "User"

WHERE age > 18

`;
```

---

## Parameterized Query

```ts
const age = 20;

await prisma.$queryRaw`

SELECT *

FROM "User"

WHERE age > ${age}

`;
```

---

# 🚨 Execute Raw SQL

Database Modify করার জন্য।

```ts
await prisma.$executeRaw`

DELETE FROM "User"

WHERE id = 10

`;
```

---

# 🏗️ Dynamic Query Building

Search থাকলে Search করবে, না থাকলে সব Data Return করবে।

```ts
const where: any = {};

if (search) {
  where.name = {
    contains: search,
    mode: "insensitive"
  };
}

const users = await prisma.user.findMany({
  where
});
```

---

# 🔄 Dynamic Sorting

```ts
await prisma.user.findMany({
  orderBy: {
    [sortField]: sortOrder
  }
});
```

---

# 📄 Dynamic Pagination

```ts
await prisma.user.findMany({
  skip: (page - 1) * limit,
  take: limit
});
```

---

# 🏢 Real World API Example

```http
GET /api/users?page=1&limit=10&search=yea&sort=name&order=asc
```

Prisma Query:

```ts
await prisma.user.findMany({
  where,
  orderBy,
  skip,
  take
});
```

---

# 🚀 Query Optimization

## ❌ Bad

```ts
include: {
  posts: true,
  profile: true,
  comments: true,
  orders: true
}
```

---

## ✅ Good

```ts
select: {
  id: true,
  name: true
}
```

---

# ⚡ Performance Tips

## ✅ Filter Database Side

❌ Bad

```ts
const users = await prisma.user.findMany();

users.filter(...)
```

---

✅ Good

```ts
await prisma.user.findMany({
  where: {
    isActive: true
  }
});
```

---

## ✅ Use Pagination

```ts
take
skip
```

---

## ✅ Create Index

Frequently Query হওয়া Field-এর উপর Index দাও।

```prisma
@@index([email])
```

---

## ✅ Select Only Required Fields

```ts
select
```

---

## ✅ Avoid N+1 Query Problem

❌ Bad

```ts
for (const user of users) {
  await prisma.post.findMany();
}
```

---

✅ Good

```ts
include: {
  posts: true
}
```

---

# 🧠 Best Practices

* ✅ Database Level Filtering ব্যবহার করো।
* ✅ Large Dataset-এর জন্য Pagination ব্যবহার করো।
* ✅ শুধুমাত্র প্রয়োজনীয় Field Fetch করো।
* ✅ Frequently Used Column-এ Index ব্যবহার করো।
* ✅ Relation Query-তে `include` ও `select` সঠিকভাবে ব্যবহার করো।
* ✅ Raw SQL শুধুমাত্র প্রয়োজন হলে ব্যবহার করো।
* ✅ Dynamic Query Builder ব্যবহার করে Flexible API তৈরি করো।

---

# 📌 Summary

এই অধ্যায়ে আমরা শিখলাম:

* ✅ Advanced Filtering
* ✅ AND / OR / NOT
* ✅ String Filtering
* ✅ Relation Filtering
* ✅ Nested Select
* ✅ Nested Include
* ✅ Aggregate
* ✅ GroupBy
* ✅ Distinct
* ✅ Offset Pagination
* ✅ Cursor Pagination
* ✅ Raw SQL
* ✅ Dynamic Query Building
* ✅ Query Optimization
* ✅ Performance Best Practices

---

# 📚 Next Part

## 🚀 Prisma Middleware & Client Extensions Deep Dive

পরবর্তী অধ্যায়ে আমরা শিখবো:

* Prisma Middleware কী?
* Middleware Lifecycle
* Query Logging
* Soft Delete Pattern
* Audit Logging
* Validation Middleware
* Prisma Client Extensions (`$extends`)
* Custom Query Methods
* Custom Result Extensions
* Performance Monitoring
* Production Best Practices

এই অধ্যায়টি শেষ করার পর তুমি Production-Level Backend Application-এ Prisma Middleware এবং Client Extension ব্যবহার করে Clean, Reusable এবং Scalable Architecture তৈরি করতে পারবে। 🚀
