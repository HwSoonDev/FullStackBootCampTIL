
### DB URL 위치 변경

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

Prisma7에서는 더이상 스키마에 url을 적지 않습니다.

```ts
//prisma.config.ts
import "dotenv/config";
import { defineConfig } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: {
    path: "prisma/migrations",
  },
  datasource: {
    url: process.env["DATABASE_URL"],
  },
});
```

대신 prisma.config.ts 파일이 추가되어 여기에 기록합니다.