`prisma/adapter-pg`는 **Prisma**가 **PostgreSQL**에 연결할 때 **Node의 pg 드라이버를 사용하도록 해주는 어댑터**입니다.

즉 한 줄로 정리하면

> **Prisma가 PostgreSQL에 접속할 때 Node의 pg 드라이버를 사용하게 해주는 연결 어댑터**

입니다.

---

# 1️⃣ 왜 필요한가

기본적으로 Prisma는 내부에 **Rust 기반 Query Engine**을 가지고 있습니다.

기본 구조

```
App
 ↓
Prisma Client
 ↓
Prisma Query Engine (Rust)
 ↓
PostgreSQL
```

그런데 어떤 환경에서는 **Node.js 드라이버 방식**을 써야 합니다.

예

- Serverless 환경
- Edge Runtime
- Cloudflare Workers
- Vercel Edge

이때 사용하는 것이

```
prisma/adapter-pg
```

입니다.

---

# 2️⃣ 구조 비교

### 기본 Prisma 방식

```
Prisma Client
      ↓
Rust Query Engine
      ↓
PostgreSQL
```

---

### prisma/adapter-pg 사용

```
Prisma Client
      ↓
pg driver
      ↓
PostgreSQL
```

여기서 `pg`는 Node의 PostgreSQL 드라이버입니다.

→ **node-postgres**

---

# 3️⃣ 언제 사용하는가

보통 이런 환경에서 사용합니다.

|환경|이유|
|---|---|
|Serverless|바이너리 실행 제한|
|Edge runtime|Rust 엔진 실행 불가|
|Cloudflare Workers|Node 환경 제한|

---

# 4️⃣ 실제 코드 예

```ts
import { PrismaClient } from '@prisma/client'
import { PrismaPg } from '@prisma/adapter-pg'
import pg from 'pg'

const pool = new pg.Pool({
  connectionString: process.env.DATABASE_URL
})

const adapter = new PrismaPg(pool)

const prisma = new PrismaClient({
  adapter
})
```

여기서

```
PrismaPg
```

가 바로 **prisma/adapter-pg**입니다.

---

# 5️⃣ 당신 프로젝트에서는 필요한가?

지금 만들고 있는 스택

```
Express
Prisma
PostgreSQL
Redis
S3
```

이라면 **대부분 필요 없습니다.**

일반 서버에서는 그냥

```ts
const prisma = new PrismaClient()
```

이게 표준입니다.

---

# 6️⃣ 정리

`prisma/adapter-pg`의 역할

|기능|설명|
|---|---|
|Prisma ↔ PostgreSQL 연결|Node pg 드라이버 사용|
|Serverless 지원|Rust Query Engine 없이 실행|
|Edge 환경 지원|Workers / Edge runtime 대응|

---

✅ **결론**

일반 서버 (Express, Nest 등)

```
❌ 필요 없음
```

Serverless / Edge

```
✅ 필요
```

---

💡 참고로 Prisma에는 **3가지 PostgreSQL 연결 방식**이 있습니다.

1️⃣ 기본 Prisma Engine  
2️⃣ `prisma/adapter-pg`  
3️⃣ **Prisma Accelerate**

이 셋의 차이를 알면 **Prisma 구조를 완전히 이해하게 됩니다.**  
원하면 이 **3개 차이를 그림으로 한번 정리해 줄게요.** (생각보다 중요합니다.)