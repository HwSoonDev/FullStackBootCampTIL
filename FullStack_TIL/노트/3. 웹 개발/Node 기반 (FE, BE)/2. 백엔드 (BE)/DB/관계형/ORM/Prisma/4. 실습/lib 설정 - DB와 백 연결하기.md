
![[Pasted image 20260328183207.png]]

백엔드에서, Prisma를 사용하려면 pg와, PrismaClient가 필요합니다.

## pg 

PostgreSQL 서버와 통신하는 드라이버(클라이언트 라이브러리
- 공식 이름 node-postgres

---
## Prisma Client

Prisma Client는 `schema.prisma`를 바탕으로 생성되는 타입이 붙은 DB 접근용 라이브러리

- `prisma.user.findMany()`, `prisma.user.create()` 같은 API로 데이터를 읽고 씁니다.
- 모델·필드 이름이 TypeScript 타입으로 잡혀 있어서 오타나 잘못된 필드를 컴파일 단계에서 많이 걸러줍니다.
- “쿼리를 어떻게 만들지·검증할지”는 Prisma 쪽이 담당하고, 실제로 DB에 연결해서 보내는 통로는 아래 어댑터(와 `pg`)가 맡습니다.

---

## Adapter (`@prisma/adapter-pg`의 `PrismaPg`)

**Adapter(드라이버 어댑터)**는 Prisma Client와 Node의 PostgreSQL 드라이버(`pg`) 사이를 잇는 브릿지입니다.

- Prisma 7에서는 Client가 `pg` 풀 같은 걸 직접 쓰지 않고, `PrismaPg`에 풀을 넘겨 “이 연결로 SQL 실행해”라고 맡기는 방식이 일반적입니다.
- 즉, Prisma = 쿼리 빌드/매핑, `pg` = PostgreSQL 프로토콜, Adapter = 둘을 연결.

```ts
const adapter = new PrismaPg(dbPool);
const prisma = new PrismaClient({ adapter });
```

여기서 `dbPool`은 `pg`의 `Pool`이고, 어댑터는 그 풀을 Prisma가 이해하는 **“SQL 실행 백엔드”**로 감싼 것입니다.

---

비유하면: Prisma Client는 “주문서(쿼리) 작성기”, `pg`는 “배달 기사”, 어댑터는 “주문서를 기사가 이해하는 형태로 넘기는 창구”에 가깝습니다.

최종형태 (요약)
```ts
const connectionString = process.env.DATABASE_URL;
if (!connectionString) {
  throw new Error("DATABASE_URL is required");
}

export const dbPool = new pg.Pool({ connectionString });
const adapter = new PrismaPg(dbPool);
const prisma = new PrismaClient({ adapter });
```

DB의 URL을 받아서,
1) DB pool을 구성하고,
2) 어댑터에 연결
3) prisma client를 생성

의 순서로 prisma를 만들어 내면, 
비로소 DB와 백이 연결되었다고 할 수 있습니다.


---

참고로, 세션 로그인의 경우 session도 prisma와 같은 DB pool을 이용합니다.
그래서, db-pool.ts와 같이 DB pool 만을 위해서 따로 분리해 사용하기도 합니다.

