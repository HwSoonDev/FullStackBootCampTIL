
**핵심 흐름만 정리해서 말하면**  
1) Prisma + PostgreSQL 설치/초기화 →  
2) `schema.prisma` 에 도메인 모델 정의 →  
3) `PrismaClient` 공용 파일 만들기 →  
4) 각 도메인 `service` 에서 `prisma` 사용하도록 수정 입니다.

---

### 1. 패키지 설치 및 초기 설정

프로젝트 루트( root )에서:

```bash
npm install @prisma/client
npm install -D prisma
```

Prisma 초기화:

```bash
npx prisma init
```

이렇게 되면:

- 루트에 `.env`
- `prisma/schema.prisma` 생성

Postgres 접속 정보 `.env` 수정 (예시):

```env
DATABASE_URL="postgresql://user:password@localhost:5432/album_maker?schema=public"
```

`package.json` 에 스크립트 하나 추가하면 편합니다:

```json
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js",
  "prisma:migrate": "prisma migrate dev",
  "prisma:studio": "prisma studio"
}
```

---

### 2. Prisma 스키마 정의 (albums / photos / users 기준)

`prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  password  String
  createdAt DateTime @default(now())
  albums    Album[]
}

model Album {
  id        String   @id @default(uuid())
  title     String
  createdAt DateTime @default(now())
  user      User     @relation(fields: [userId], references: [id])
  userId    String
  photos    Photo[]
}

model Photo {
  id        String   @id @default(uuid())
  album     Album    @relation(fields: [albumId], references: [id])
  albumId   String
  url       String
  createdAt DateTime @default(now())
}
```

마이그레이션 실행:

```bash
npx prisma migrate dev --name init
```

---

### 3. PrismaClient 공용 파일 만들기

`src/db/prisma.ts` (새 파일):

```ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as {
  prisma?: PrismaClient;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ["query", "error", "warn"],
  });

if (process.env.NODE_ENV !== "production") {
  globalForPrisma.prisma = prisma;
}
```

이제 어디서든 `import { prisma } from "../db/prisma";` 식으로 사용.

---

### 4. albums 서비스에 Prisma 적용 예시

`src/modules/albums/albums.model.ts` 는 이제 Prisma 타입을 그대로 쓰거나, 필요하면 래핑 타입만 남겨도 됩니다. 간단히 Prisma 타입 사용 예시:

```ts
// 필요 없다면 이 파일은 삭제하거나 Prisma 타입만 re-export 해도 됨
export type { Album } from "@prisma/client";
```

`src/modules/albums/albums.service.ts` 를 Postgres 기반으로 변경:

```ts
import type { Album } from "@prisma/client";
import { prisma } from "../../db/prisma";

export async function listAlbums(): Promise<Album[]> {
  return prisma.album.findMany({
    orderBy: { createdAt: "desc" },
  });
}

interface CreateAlbumInput {
  title: string;
  userId: string;
}

export async function createAlbum(input: CreateAlbumInput): Promise<Album> {
  return prisma.album.create({
    data: {
      title: input.title,
      userId: input.userId,
    },
  });
}
```

컨트롤러에서 새 앨범 생성까지 하고 싶다면 (예시):

```ts
// src/modules/albums/albums.controller.ts
import type { Request, Response } from "express";
import { listAlbums, createAlbum } from "./albums.service";

export async function getAlbums(_req: Request, res: Response) {
  const albums = await listAlbums();
  res.json({ albums });
}

export async function postAlbum(req: Request, res: Response) {
  const { title, userId } = req.body;
  const album = await createAlbum({ title, userId });
  res.status(201).json({ album });
}
```

그리고 라우트에 POST 추가:

```ts
// src/api/albums/albums.routes.ts
import { Router } from "express";
import { getAlbums, postAlbum } from "../../modules/albums/albums.controller";

export const albumsRouter = Router();

albumsRouter.get("/", getAlbums);
albumsRouter.post("/", postAlbum);
```

---

### 5. photos, auth 도메인도 같은 패턴

- `photos.service.ts` → `prisma.photo.findMany`, `create`, `delete` 등 사용
- `auth.service.ts` → `prisma.user.findUnique({ where: { email } })` 로 유저 조회 후 비밀번호 검증, 토큰 발급

---

원하시면 다음 단계로,  
- 실제 `User` 테이블을 기반으로 **로그인/회원가입 흐름**(비밀번호 해싱, JWT 발급)까지 Prisma와 엮어서 코드로 만들어 줄게요.