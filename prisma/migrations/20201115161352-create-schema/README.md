# Migration `20201115161352-create-schema`

This migration has been generated by Ahmed Hadjou at 11/15/2020, 4:13:52 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TYPE "public"."UserRole" AS ENUM ('USER', 'ADMIN', 'SUPERADMIN')

CREATE TABLE "users" (
"id" SERIAL,
    "uuid" TEXT NOT NULL,
    "email" TEXT NOT NULL,
    "name" TEXT NOT NULL,
    "role" "UserRole" NOT NULL DEFAULT E'USER',
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL,

    PRIMARY KEY ("id")
)

CREATE TABLE "posts" (
"id" SERIAL,
    "uuid" TEXT NOT NULL,
    "title" TEXT NOT NULL,
    "body" TEXT,
    "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP(3) NOT NULL,
    "userId" INTEGER NOT NULL,

    PRIMARY KEY ("id")
)

CREATE UNIQUE INDEX "users.uuid_unique" ON "users"("uuid")

CREATE UNIQUE INDEX "users.email_unique" ON "users"("email")

CREATE UNIQUE INDEX "posts.uuid_unique" ON "posts"("uuid")

ALTER TABLE "posts" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201115161352-create-schema
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,38 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "postgresql"
+  url = "***"
+}
+
+model User {
+  @@map(name: "users")
+  id        Int      @default(autoincrement()) @id
+  uuid      String   @default(uuid()) @unique
+  email     String   @unique
+  name      String
+  role      UserRole @default(USER)
+  createdAt DateTime @default(now()) @map("created_at")
+  updatedAt DateTime @updatedAt @map("updated_at")
+  posts     Post[]
+}
+
+model Post {
+  @@map(name: "posts")
+  id        Int      @default(autoincrement()) @id
+  uuid      String   @default(uuid()) @unique
+  title     String
+  body      String?
+  user      User     @relation(fields: [userId], references: [id])
+  createdAt DateTime @default(now()) @map("created_at")
+  updatedAt DateTime @updatedAt @map("updated_at")
+  userId    Int
+}
+
+enum UserRole {
+  USER
+  ADMIN
+  SUPERADMIN
+}
```

