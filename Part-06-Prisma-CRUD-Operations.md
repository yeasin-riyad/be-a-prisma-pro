# 🚀 Prisma Part 6: CRUD Operations Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের ষষ্ঠ পর্ব।

---

# 📖 CRUD কী?

CRUD এর পূর্ণরূপ হলো:

| Operation | Description                     |
| --------- | ------------------------------- |
| **C**     | Create (নতুন Data তৈরি করা)     |
| **R**     | Read (Data পড়া বা Retrieve করা) |
| **U**     | Update (Data পরিবর্তন করা)      |
| **D**     | Delete (Data মুছে ফেলা)         |

Prisma Client ব্যবহার করে খুব সহজেই এই Operations করা যায়।

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Prisma Client Setup
* Create
* CreateMany
* FindUnique
* FindFirst
* FindMany
* Select
* Include
* Filtering
* Sorting
* Pagination
* Update
* UpdateMany
* Upsert
* Delete
* DeleteMany
* Count
* Aggregate
* GroupBy
* Transactions
* Best Practices

---

# 📦 Sample Model

এই অধ্যায়ের সকল উদাহরণ নিচের Model-এর উপর ভিত্তি করে।

```prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  age       Int?
  isActive  Boolean  @default(true)

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

---

# 🚀 Prisma Client Setup

```javascript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();
```

---

# ✨ Create

একটি নতুন Record তৈরি করতে `create()` ব্যবহার করা হয়।

```javascript
const user = await prisma.user.create({
  data: {
    name: "Yeasin",
    email: "yeasin@gmail.com",
    age: 24
  }
});

console.log(user);
```

---

## Output

```json
{
  "id": 1,
  "name": "Yeasin",
  "email": "yeasin@gmail.com",
  "age": 24
}
```

---

# 📚 CreateMany

একসাথে একাধিক Record Insert করার জন্য।

```javascript
await prisma.user.createMany({
  data: [
    {
      name: "John",
      email: "john@gmail.com"
    },
    {
      name: "Alice",
      email: "alice@gmail.com"
    }
  ]
});
```

---

## Skip Duplicate Records

```javascript
await prisma.user.createMany({
  data: users,
  skipDuplicates: true
});
```

---

# 🔍 FindUnique

Unique Field ব্যবহার করে একটি Record খুঁজে বের করা হয়।

```javascript
const user = await prisma.user.findUnique({
  where: {
    email: "yeasin@gmail.com"
  }
});
```

---

# 🔎 FindFirst

Condition অনুযায়ী প্রথম Record Return করে।

```javascript
const user = await prisma.user.findFirst({
  where: {
    isActive: true
  }
});
```

---

# 📋 FindMany

সব Record অথবা Condition অনুযায়ী একাধিক Record Fetch করতে ব্যবহৃত হয়।

```javascript
const users = await prisma.user.findMany();
```

---

# 🎯 Filtering

## Equal

```javascript
const users = await prisma.user.findMany({
  where: {
    age: 25
  }
});
```

---

## Greater Than

```javascript
where: {
  age: {
    gt: 18
  }
}
```

---

## Greater Than or Equal

```javascript
gte
```

---

## Less Than

```javascript
lt
```

---

## Less Than or Equal

```javascript
lte
```

---

## Not Equal

```javascript
not
```

---

# 🔤 String Filtering

## Contains

```javascript
where: {
  name: {
    contains: "Yea"
  }
}
```

---

## StartsWith

```javascript
startsWith: "Ye"
```

---

## EndsWith

```javascript
endsWith: "der"
```

---

# 🔗 AND Query

```javascript
where: {
  AND: [
    {
      age: {
        gt: 20
      }
    },
    {
      isActive: true
    }
  ]
}
```

---

# 🔀 OR Query

```javascript
where: {
  OR: [
    {
      age: 18
    },
    {
      age: 20
    }
  ]
}
```

---

# 🎯 Select Specific Fields

শুধুমাত্র প্রয়োজনীয় Field Fetch করার জন্য।

```javascript
const users = await prisma.user.findMany({
  select: {
    id: true,
    name: true
  }
});
```

---

## Output

```json
[
  {
    "id": 1,
    "name": "Yeasin"
  }
]
```

---

# 🔗 Include Relations

Relation সহ Data Fetch করার জন্য।

```javascript
const users = await prisma.user.findMany({
  include: {
    posts: true
  }
});
```

---

# 📈 Sorting

## Ascending

```javascript
orderBy: {
  name: "asc"
}
```

---

## Descending

```javascript
orderBy: {
  createdAt: "desc"
}
```

---

# 📄 Pagination

## Skip + Take

```javascript
const users = await prisma.user.findMany({
  skip: 10,
  take: 10
});
```

---

## Example

```text
Page 1

skip = 0
take = 10

----------------

Page 2

skip = 10
take = 10

----------------

Page 3

skip = 20
take = 10
```

---

# ✏️ Update

একটি নির্দিষ্ট Record Update করতে।

```javascript
await prisma.user.update({
  where: {
    email: "yeasin@gmail.com"
  },

  data: {
    name: "Yeasin Mazumder"
  }
});
```

---

# 📝 UpdateMany

একাধিক Record একসাথে Update করতে।

```javascript
await prisma.user.updateMany({
  where: {
    isActive: false
  },

  data: {
    isActive: true
  }
});
```

---

# ➕

Increment Value

```javascript
data: {
  age: {
    increment: 1
  }
}
```

---

# ➖

Decrement Value

```javascript
data: {
  age: {
    decrement: 1
  }
}
```

---

# 🔄 Upsert

Record থাকলে Update করবে, না থাকলে Create করবে।

```javascript
await prisma.user.upsert({
  where: {
    email: "admin@gmail.com"
  },

  update: {
    name: "Admin"
  },

  create: {
    name: "Admin",
    email: "admin@gmail.com"
  }
});
```

---

# ❌ Delete

একটি Record Delete করতে।

```javascript
await prisma.user.delete({
  where: {
    email: "john@gmail.com"
  }
});
```

---

# 🗑️ DeleteMany

একাধিক Record Delete করতে।

```javascript
await prisma.user.deleteMany({
  where: {
    isActive: false
  }
});
```

---

# 🔢 Count

মোট Record সংখ্যা বের করতে।

```javascript
const total = await prisma.user.count();
```

---

# 📊 Aggregate

Average, Maximum, Minimum ইত্যাদি বের করতে।

```javascript
const result = await prisma.user.aggregate({
  _avg: {
    age: true
  },

  _max: {
    age: true
  },

  _min: {
    age: true
  }
});
```

---

# 📂 GroupBy

একই ধরনের Data Group করতে।

```javascript
const users = await prisma.user.groupBy({
  by: ["isActive"],
  _count: true
});
```

---

# 🔒 Transactions

একাধিক Query একসাথে Execute করার জন্য।

```javascript
await prisma.$transaction([
  prisma.user.create({
    data: {
      name: "John",
      email: "john@gmail.com"
    }
  }),

  prisma.profile.create({
    data: {
      bio: "Developer"
    }
  })
]);
```

---

# ⚡ Interactive Transaction

```javascript
await prisma.$transaction(async (tx) => {

  const user = await tx.user.create({
    data: {
      name: "Yeasin",
      email: "yeasin@gmail.com"
    }
  });

  await tx.profile.create({
    data: {
      bio: "Full Stack Developer",
      userId: user.id
    }
  });

});
```

---

# 🧠 Best Practices

## ✅ Use `select`

```javascript
select: {
  id: true,
  name: true
}
```

শুধুমাত্র প্রয়োজনীয় Data Fetch করো।

---

## ✅ Use Pagination

```javascript
skip
take
```

Large Dataset-এর জন্য অবশ্যই Pagination ব্যবহার করো।

---

## ✅ Use Transactions

যখন একাধিক Table Update করতে হবে।

---

## ✅ Use Include Carefully

```javascript
include: {
  posts: true
}
```

অপ্রয়োজনীয় Relation Load করা থেকে বিরত থাকো।

---

## ✅ Filter on Database Side

```javascript
where: {
  isActive: true
}
```

JavaScript-এ Filter করার পরিবর্তে Database-এ Filter করো।

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

* ✅ Create
* ✅ CreateMany
* ✅ FindUnique
* ✅ FindFirst
* ✅ FindMany
* ✅ Select
* ✅ Include
* ✅ Filtering
* ✅ Sorting
* ✅ Pagination
* ✅ Update
* ✅ UpdateMany
* ✅ Upsert
* ✅ Delete
* ✅ DeleteMany
* ✅ Count
* ✅ Aggregate
* ✅ GroupBy
* ✅ Transactions

---

# 📚 Next Part

## Prisma Migrations Deep Dive

পরবর্তী পর্বে আমরা শিখবো:

* Prisma Migrate কী?
* migrate dev
* migrate deploy
* migrate reset
* db push
* db pull
* Migration History
* Production Migration Strategy
* Best Practices

Prisma ব্যবহার করে Database Version Control শেখার জন্য এটি অত্যন্ত গুরুত্বপূর্ণ একটি অধ্যায়। 🚀
