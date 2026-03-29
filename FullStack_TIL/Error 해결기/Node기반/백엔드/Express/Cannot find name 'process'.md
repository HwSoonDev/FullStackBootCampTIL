// prisma.config.ts
![[Pasted image 20260328172037.png]]

환경변수를 읽기 위해 중요한 process 객체에 접근할 수 없었습니다.
process는 **Node.js가 런타임에 제공하는 전역 객체**입니다.

TypeScript에서 `Cannot find name 'process'`가 나오는 이유는, 런타임에는 Node가 `process`를 주지만, 타입 검사기는 `@types/node` 같은 타입 정의를 봐야 `process`가 무엇인지 알 수 있기 때문입니다.

1) node패키지
2) @types/node

둘 다 잘 설치되어 있는데도 해당 문제가 발생한다면,
프로젝트 루트에 있는 tsconfig.json의 설정 문제 일 수도 있습니다.

필자의 프로젝트 폴더 구조는 현재 이렇습니다.
```
back-end/
├── .env
├── .gitignore
├── package.json
├── package-lock.json
├── tsconfig.json
├── prisma.config.ts
├── prisma/
│   └── schema.prisma
└── src/
front-end
```

그리고 tsconfig.json의 구조는 이렇습니다.
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "types": ["node"],
    "rootDir": "src",
    "outDir": "dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "resolveJsonModule": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "dist"]
}
```

즉, TypeScript가 추적하는 src/ 폴더 아래에 위치하지 않기 때문에,
prsima.config.ts는 타입 체크를 받을 수 없고, 에디터 기준에서 process를 찾을 수 없는 것입니다.

=> `TypeScript로 인정 받지 못한 파일`

그러면 tsconfig의 include 설정에 prisma.config.ts를 추가 할 수도 있겠지만,
그러면 다시 루트 디렉토리 설정과 충돌합니다. (src/)

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "types": ["node"],
    "rootDir": "src", // 충돌 !
    "outDir": "dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "resolveJsonModule": true
  },
  "include": ["src/**/*.ts", "prisma.config.ts"], // 충돌 !
  "exclude": ["node_modules", "dist"]
}
```

하지만 전 루트를 src로 고정하고 싶습니다.

때문에 꼼수를 썼습니다.
ts 파일 상단에 `/// <reference types="node" />`를 추가하는 것입니다.
그러면 tsconfig.json에 포함 시키지 않아도 Node를 기준으로 타입 검사를 해줍니다.

```ts
/// <reference types="node" />

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

이제 process가 뭔지 에디터는 알 수 있습니다.

![[Pasted image 20260328175800.png]]