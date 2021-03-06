# Migration `20201029091035-init`

This migration has been generated by smakosh at 10/29/2020, 10:10:35 AM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
ALTER TABLE "public"."Profile" DROP CONSTRAINT "Profile_userId_fkey"

ALTER TABLE "public"."Tag" DROP CONSTRAINT "Tag_postId_fkey"

ALTER TABLE "public"."Tag" DROP COLUMN "postId"

CREATE TABLE "public"."_PostToTag" (
"A" integer   NOT NULL ,
"B" integer   NOT NULL 
)

DROP TABLE "public"."Profile"

CREATE UNIQUE INDEX "_PostToTag_AB_unique" ON "public"."_PostToTag"("A", "B")

CREATE INDEX "_PostToTag_B_index" ON "public"."_PostToTag"("B")

ALTER TABLE "public"."_PostToTag" ADD FOREIGN KEY("A")REFERENCES "public"."Post"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "public"."_PostToTag" ADD FOREIGN KEY("B")REFERENCES "public"."Tag"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER INDEX "public"."User.email" RENAME TO "User.email_unique"
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20200414104043-initial-migration..20201029091035-init
--- datamodel.dml
+++ datamodel.dml
@@ -3,39 +3,30 @@
 }
 datasource db {
   provider = "postgresql"
-  url = "***"
+  url = "***"
 }
 model Post {
+  id        Int      @id @default(autoincrement())
   authorId  Int
   content   String?
   createdAt DateTime @default(now())
-  id        Int      @default(autoincrement()) @id
   published Boolean  @default(false)
   title     String
   User      User     @relation(fields: [authorId], references: [id])
-  Tag       Tag[]
+  tags      Tag[]
 }
-model Profile {
-  bio    String?
-  id     Int     @default(autoincrement()) @id
-  userId Int     @unique
-  User   User    @relation(fields: [userId], references: [id])
-}
-
 model Tag {
-  id     Int    @default(autoincrement()) @id
-  name   String
-  postId Int
-  Post   Post   @relation(fields: [postId], references: [id])
+  id    Int    @id @default(autoincrement())
+  name  String
+  posts Post[]
 }
 model User {
-  email   String   @unique
-  id      Int      @default(autoincrement()) @id
-  name    String?
-  Post    Post[]
-  Profile Profile?
-}
+  email String  @unique
+  id    Int     @id @default(autoincrement())
+  name  String?
+  posts Post[]
+}
```


