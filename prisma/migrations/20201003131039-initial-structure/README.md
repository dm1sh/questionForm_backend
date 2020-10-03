# Migration `20201003131039-initial-structure`

This migration has been generated by Dm1tr1y147 at 10/3/2020, 6:10:39 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "User" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "name" TEXT NOT NULL,
    "email" TEXT,
    "createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
)

CREATE TABLE "Poll" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "userId" INTEGER,
    "title" TEXT NOT NULL,
    "description" TEXT,
    "slug" TEXT NOT NULL,
    "createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Question" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "pollId" INTEGER,
    "title" TEXT NOT NULL,

    FOREIGN KEY ("pollId") REFERENCES "Poll"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Variant" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "questionId" INTEGER,
    "text" TEXT NOT NULL,
    "count" INTEGER NOT NULL DEFAULT 0,

    FOREIGN KEY ("questionId") REFERENCES "Question"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE UNIQUE INDEX "Poll.slug_unique" ON "Poll"("slug")
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201003131039-initial-structure
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,46 @@
+// This is your Prisma schema file,
+// learn more about it in the docs: https://pris.ly/d/prisma-schema
+
+datasource db {
+  provider = "sqlite"
+  url = "***"
+}
+
+generator client {
+  provider = "prisma-client-js"
+}
+
+model User {
+  id        Int      @id @default(autoincrement())
+  name      String
+  email     String?
+  createdAt DateTime @default(now())
+  polls     Poll[]
+}
+
+model Poll {
+  id          Int        @id @default(autoincrement())
+  User        User?      @relation(fields: [userId], references: [id])
+  userId      Int?
+  title       String
+  description String?
+  slug        String     @unique
+  questions   Question[]
+  createdAt   DateTime @default(now())
+}
+
+model Question {
+  id       Int      @id @default(autoincrement())
+  Poll     Poll?    @relation(fields: [pollId], references: [id])
+  pollId   Int?
+  title    String
+  variants Variant[]
+}
+
+model Variant {
+  id         Int       @id @default(autoincrement())
+  Question   Question? @relation(fields: [questionId], references: [id])
+  questionId Int?
+  text       String
+  count      Int       @default(0)
+}
```

