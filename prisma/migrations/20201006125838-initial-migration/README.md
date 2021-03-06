# Migration `20201006125838-initial-migration`

This migration has been generated by Dm1tr1y147 at 10/6/2020, 5:58:38 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TYPE "public"."ChoiseType" AS ENUM ('SELECT', 'CHECK', 'CHOOSE')

CREATE TYPE "public"."AnswerType" AS ENUM ('INPUT', 'CHOISE')

CREATE TABLE "public"."Form" (
"title" text   NOT NULL ,
"dateCreated" timestamp(3)   NOT NULL DEFAULT CURRENT_TIMESTAMP,
"id" integer   NOT NULL ,
"userId" integer   NOT NULL ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."ChoisesQuestion" (
"title" text   NOT NULL ,
"type" "ChoiseType"  NOT NULL ,
"number" integer   NOT NULL ,
"id" integer   NOT NULL ,
"formId" integer   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."Variant" (
"text" text   NOT NULL ,
"id" integer   NOT NULL ,
"choisesQuestionId" integer   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."InputQuestion" (
"title" text   NOT NULL ,
"number" integer   NOT NULL ,
"id" integer   NOT NULL ,
"formId" integer   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."FormSubmission" (
"date" timestamp(3)   NOT NULL DEFAULT CURRENT_TIMESTAMP,
"id" integer   NOT NULL ,
"formId" integer   ,
"userId" integer   NOT NULL ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."Answer" (
"id" integer   NOT NULL ,
"userInput" text   NOT NULL ,
"userChoise" integer   NOT NULL ,
"type" "AnswerType"  NOT NULL ,
"formSubmissionId" integer   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."User" (
"id" integer   NOT NULL ,
"name" text   NOT NULL ,
PRIMARY KEY ("id")
)

ALTER TABLE "public"."Form" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "public"."ChoisesQuestion" ADD FOREIGN KEY ("formId")REFERENCES "public"."Form"("id") ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE "public"."Variant" ADD FOREIGN KEY ("choisesQuestionId")REFERENCES "public"."ChoisesQuestion"("id") ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE "public"."InputQuestion" ADD FOREIGN KEY ("formId")REFERENCES "public"."Form"("id") ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE "public"."FormSubmission" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "public"."FormSubmission" ADD FOREIGN KEY ("formId")REFERENCES "public"."Form"("id") ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE "public"."Answer" ADD FOREIGN KEY ("formSubmissionId")REFERENCES "public"."FormSubmission"("id") ON DELETE SET NULL ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201006125838-initial-migration
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,126 @@
+// This is your Prisma schema file,
+// learn more about it in the docs: https://pris.ly/d/prisma-schema
+
+datasource db {
+  provider = "postgres"
+  url = "***"
+}
+
+generator client {
+  provider = "prisma-client-js"
+}
+
+// model User {
+//   id        Int      @id @default(autoincrement())
+//   name      String
+//   email     String?
+//   createdAt DateTime @default(now())
+//   polls     Poll[]
+// }
+
+// model Poll {
+//   id          Int        @id @default(autoincrement())
+//   User        User?      @relation(fields: [userId], references: [id])
+//   userId      Int?
+//   title       String
+//   description String?
+//   slug        String     @unique @default(cuid())
+//   questions   Question[]
+//   createdAt   DateTime   @default(now())
+// }
+
+// model Question {
+//   id       Int       @id @default(autoincrement())
+//   Poll     Poll?     @relation(fields: [pollId], references: [id])
+//   pollId   Int?
+//   title    String
+//   variants Variant[]
+// }
+
+// model Variant {
+//   id         Int       @id @default(autoincrement())
+//   Question   Question? @relation(fields: [questionId], references: [id])
+//   questionId Int?
+//   text       String
+//   count      Int       @default(0)
+// }
+
+model Form {
+  title            String
+  choisesQuestions ChoisesQuestion[]
+  inputQuestions   InputQuestion[]
+  submissions      FormSubmission[]
+  dateCreated      DateTime          @default(now())
+
+  id     Int  @id
+  userId Int
+  author User @relation(fields: [userId], references: [id])
+}
+
+model ChoisesQuestion {
+  title    String
+  variants Variant[]
+  type     ChoiseType
+  number   Int
+
+  id     Int   @id
+  Form   Form? @relation(fields: [formId], references: [id])
+  formId Int?
+}
+
+model Variant {
+  text String
+
+  id                Int              @id
+  ChoisesQuestion   ChoisesQuestion? @relation(fields: [choisesQuestionId], references: [id])
+  choisesQuestionId Int?
+}
+
+model InputQuestion {
+  title  String
+  number Int
+
+  id     Int   @id
+  Form   Form? @relation(fields: [formId], references: [id])
+  formId Int?
+}
+
+model FormSubmission {
+  answers Answer[]
+  date    DateTime @default(now())
+
+
+  id     Int   @id
+  user   User  @relation(fields: [userId], references: [id])
+  Form   Form? @relation(fields: [formId], references: [id])
+  formId Int?
+  userId Int
+}
+
+model Answer {
+  id               Int             @id
+  userInput        String
+  userChoise       Int
+  type             AnswerType
+  FormSubmission   FormSubmission? @relation(fields: [formSubmissionId], references: [id])
+  formSubmissionId Int?
+}
+
+enum ChoiseType {
+  SELECT
+  CHECK
+  CHOOSE
+}
+
+enum AnswerType {
+  INPUT
+  CHOISE
+}
+
+model User {
+  id   Int    @id
+  name String
+
+  forms            Form[]
+  formsSubmissions FormSubmission[]
+}
```


