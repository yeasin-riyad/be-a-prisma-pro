# 🚀 Prisma Part 5: Prisma Relations Deep Dive (বাংলা)

> Prisma শেখার Official Documentation Based সিরিজের পঞ্চম পর্ব।

---

# 📖 Prisma Relations কী?

বাস্তব Database-এ Table গুলো একে অপরের সাথে সম্পর্কিত থাকে।

উদাহরণ:

* একজন User-এর একটি Profile থাকে
* একজন User অনেক Post লিখতে পারে
* একজন Student অনেক Course-এ Enroll করতে পারে

Prisma-তে এই সম্পর্কগুলোকে **Relations** বলা হয়।

---

# 🎯 Learning Goals

এই পর্বে আমরা শিখবো:

* Foreign Key
* One-to-One Relation
* One-to-Many Relation
* Many-to-Many Relation
* Implicit Many-to-Many
* Explicit Many-to-Many
* Relation Queries
* Nested Writes
* Connect & Disconnect
* Cascade Delete

---

# 🔑 Foreign Key কী?

Foreign Key হলো একটি Table-এর Field যা অন্য Table-এর Primary Key-কে Reference করে।

---

## Example

### User Model

```prisma
model User {
  id   Int    @id @default(autoincrement())
  name String
}
```

### Post Model

```prisma
model Post {
  id     Int    @id @default(autoincrement())
  title  String

  userId Int
}
```

এখানে:

```prisma
userId Int
```

Foreign Key হিসেবে কাজ করবে।

---

# 1️⃣ One-to-One Relation

একটি Record-এর সাথে অন্য Table-এর শুধুমাত্র একটি Record সম্পর্কিত থাকবে।

---

## Real World Example

```text
User
  ↔
Profile
```

একজন User-এর একটি Profile থাকবে।

---

## Prisma Schema

```prisma
model User {
  id      Int      @id @default(autoincrement())
  name    String

  profile Profile?
}

model Profile {
  id      Int    @id @default(autoincrement())
  bio     String

  userId  Int    @unique

  user User @relation(
    fields: [userId],
    references: [id]
  )
}
```

---

## Why `@unique`?

```prisma
userId Int @unique
```

কারণ একজন User-এর একটির বেশি Profile থাকা যাবে না।

---

## Database Diagram

```text
User
 ├── id
 └── name

     1
     │
     │
     1

Profile
 ├── id
 ├── bio
 └── userId
```

---

# 2️⃣ One-to-Many Relation

সবচেয়ে বেশি ব্যবহৃত Relation।

---

## Real World Example

```text
User
 │
 ├── Post
 ├── Post
 └── Post
```

একজন User অনেক Post লিখতে পারে।

---

## Prisma Schema

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String

  posts Post[]
}

model Post {
  id      Int    @id @default(autoincrement())
  title   String

  userId  Int

  author User @relation(
    fields: [userId],
    references: [id]
  )
}
```

---

## Explanation

### User Side

```prisma
posts Post[]
```

একজন User-এর অনেক Post থাকতে পারে।

---

### Post Side

```prisma
userId Int
```

Foreign Key

```prisma
author User @relation(...)
```

Relation Definition

---

## Database Diagram

```text
User
 ├── id
 └── name

    1
    │
    │
    ∞

Post
 ├── id
 ├── title
 └── userId
```

---

# 3️⃣ Many-to-Many Relation

একাধিক Record একাধিক Record-এর সাথে সম্পর্কিত হতে পারে।

---

## Real World Example

```text
Student
    ↔
Course
```

একজন Student অনেক Course নিতে পারে।

একটি Course অনেক Student নিতে পারে।

---

# Implicit Many-to-Many Relation

Prisma স্বয়ংক্রিয়ভাবে Join Table তৈরি করবে।

```prisma
model Student {
  id      Int      @id @default(autoincrement())
  name    String

  courses Course[]
}

model Course {
  id       Int       @id @default(autoincrement())
  title    String

  students Student[]
}
```

---

## Internally Prisma Creates

```text
_StudentToCourse
```

Join Table

---

# Explicit Many-to-Many Relation

যখন Join Table-এ অতিরিক্ত Data রাখতে হয়।

---

## Example

```text
Enrollment
 ├── studentId
 ├── courseId
 └── enrolledAt
```

---

## Prisma Schema

```prisma
model Student {
  id          Int @id @default(autoincrement())
  name        String

  enrollments Enrollment[]
}

model Course {
  id          Int @id @default(autoincrement())
  title       String

  enrollments Enrollment[]
}

model Enrollment {
  studentId  Int
  courseId   Int

  enrolledAt DateTime @default(now())

  student Student @relation(
    fields: [studentId],
    references: [id]
  )

  course Course @relation(
    fields: [courseId],
    references: [id]
  )

  @@id([studentId, courseId])
}
```

---

# 🔍 Query Relations

---

## Get User With Posts

```js
const users = await prisma.user.findMany({
  include: {
    posts: true
  }
});
```

---

## Result

```json
[
  {
    "id": 1,
    "name": "Yeasin",
    "posts": [
      {
        "id": 1,
        "title": "Prisma Tutorial"
      }
    ]
  }
]
```

---

## Get Posts With Author

```js
const posts = await prisma.post.findMany({
  include: {
    author: true
  }
});
```

---

# 🔥 Nested Writes

Prisma-এর অন্যতম Powerful Feature।

---

## Create User With Profile

```js
await prisma.user.create({
  data: {
    name: "Yeasin",

    profile: {
      create: {
        bio: "Full Stack Developer"
      }
    }
  }
});
```

এক Query-তেই:

* User Create
* Profile Create

দুটোই হবে।

---

## Create User With Posts

```js
await prisma.user.create({
  data: {
    name: "Yeasin",

    posts: {
      create: [
        {
          title: "Post 1"
        },
        {
          title: "Post 2"
        }
      ]
    }
  }
});
```

---

# 🔗 Connect Existing Records

ধরো User আগে থেকেই Database-এ আছে।

```js
await prisma.post.create({
  data: {
    title: "New Post",

    author: {
      connect: {
        id: 1
      }
    }
  }
});
```

---

# 🔌 Disconnect Relation

```js
await prisma.profile.update({
  where: {
    id: 1
  },

  data: {
    user: {
      disconnect: true
    }
  }
});
```

---

# ❌ Cascade Delete

Parent Record Delete হলে Child Record-ও Delete হবে কিনা তা Control করা যায়।

---

## Example

```prisma
model Post {
  id      Int @id @default(autoincrement())

  userId  Int

  author User @relation(
    fields: [userId],
    references: [id],
    onDelete: Cascade
  )
}
```

---

## Behaviour

```text
Delete User
      │
      ▼
Delete All Posts
```

---

# Other Delete Strategies

## Cascade

```prisma
onDelete: Cascade
```

Parent Delete → Child Delete

---

## Restrict

```prisma
onDelete: Restrict
```

Parent Delete Block হবে।

---

## SetNull

```prisma
onDelete: SetNull
```

Foreign Key NULL হয়ে যাবে।

---

## NoAction

```prisma
onDelete: NoAction
```

Database Default Behaviour Follow করবে।

---

# 🧠 Best Practices

## Always Use Meaningful Relation Names

```prisma
author User
```

---

## Keep Foreign Keys Explicit

```prisma
userId Int
```

---

## Use Cascade Carefully

```prisma
onDelete: Cascade
```

Production Application-এ Data Loss হতে পারে।

---

## Prefer Explicit Many-to-Many

যদি Join Table-এ অতিরিক্ত Data রাখতে হয়।

```prisma
Enrollment
```

Pattern ব্যবহার করো।

---

# 🏢 Real World Example

## Blog System

```text
User
 │
 ├── Profile
 │
 └── Posts
      │
      └── Comments
```

---

## E-commerce System

```text
User
 │
 ├── Orders
 │
 └── Reviews

Product
 │
 ├── Reviews
 │
 └── Categories
```

---

# 📌 Summary

এই পর্বে আমরা শিখলাম:

✅ Foreign Key

✅ One-to-One Relation

✅ One-to-Many Relation

✅ Many-to-Many Relation

✅ Implicit Many-to-Many

✅ Explicit Many-to-Many

✅ Relation Queries

✅ Nested Writes

✅ Connect & Disconnect

✅ Cascade Delete

---

# 📚 Next Part

## Prisma CRUD Operations Deep Dive

পরবর্তী পর্বে আমরা Prisma Client ব্যবহার করে শিখবো:

* Create
* CreateMany
* FindMany
* FindUnique
* Update
* UpdateMany
* Delete
* DeleteMany
* Upsert

এবং বাস্তব Project-এর CRUD Examples দেখবো। 🚀
