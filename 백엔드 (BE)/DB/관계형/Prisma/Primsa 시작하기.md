prisma를 시작하기 위해서는 노드 패키지를 실행해야 한다.
(npx = node pakage executer. 노드 패키지 실행기)

PostgreSQL 줄여서, Postgres.

prisma에서 PostgreSQL을 실행하는 코드다.

```bash
npx prisma init --datasource-provider postgresql
```

postgres URL 설정하기
![[Pasted image 20250818114131.png]]

나는 윈도우이기 때문에

**유저 네임:** postgres
**비밀번호:** 설치 시 설정한 비밀번호

를 URL에 넣어 준다.

```env
DATABASE_URL="postgresql://postgres:password@lacalhost:5432/comazon_dev?shema=public"
PORT=3000
```
로컬 호스트가 5432인 이유는 설치 시 초기 설정을 그대로 5432로 뒀기 때문.

prisma 폴더 안에 schema.prisma 안에는 클라이언트와 DB에 대한 설정이 기록되어 있다.

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```
