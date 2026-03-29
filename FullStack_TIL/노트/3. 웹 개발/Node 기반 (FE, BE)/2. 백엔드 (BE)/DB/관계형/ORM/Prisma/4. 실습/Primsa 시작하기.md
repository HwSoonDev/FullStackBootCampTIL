
## Prisma 설치 방법 (Node.js / TypeScript)

`back-end`처럼 npm을 쓰는 경우 아래 순서로 하면 됩니다.

### 1. 패키지 설치

프로젝트 루트(예: `back-end`)에서:

```bash
npm install @prisma/client 
npm install prisma --save-dev
```

- **`@prisma/client`**: 앱에서 쿼리할 때 쓰는 런타임 클라이언트
- **`prisma`**: CLI, 스키마, 마이그레이션용 (개발 의존성으로 두는 경우가 많음)

`pnpm`이면 `pnpm add @prisma/client` / `pnpm add -D prisma`, 
`yarn`이면 `yarn add @prisma/client` / `yarn add -D prisma`
로 같은 역할입니다.
	
### 2. Prisma 초기화

```bash
npx prisma init --datasource-provider postgresql
```

`--datasource-provider postgresql`를 추가하면,
기본 DBMS를 postgresql로 설정해주고, .env에도 DATABASE_URL 예시를 적어 줍니다.


- `prisma/schema.prisma` 생성
- `.env`에 `DATABASE_URL` 예시 추가 (없으면 생성)

`.env`의 `DATABASE_URL`을 실제 DB에 맞게 수정합니다. (PostgreSQL, MySQL, SQLite 등)

#### 1) .env DB URL 설정
![[Pasted image 20250818114131.png]]
#### 2) shema.prisma 설정

```
//schema.prisma
generator client {
  provider = "prisma-client-js"
  output   = "../src/generated/prisma"
}

datasource db {
  provider = "postgresql"
}
```

[[Prisma7 변경점]]
### 3. 스키마 작성 후 클라이언트 생성

`prisma/schema.prisma`에 `model`을 정의한 뒤:

```bash
npx prisma generate
```

DB에 반영하려면 (선택):

```bash
npx prisma migrate dev --name init
```

### 4. 코드에서 사용 (ESM 예시)

```ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();
```