
## 전황

고급 프로젝트에서

기사님 테이블에 속해 있는 연관 테이블의 정보를 기준으로 정렬을 해야하는 일이 있었다.

정확한 조건은 잘 모르겠지만, 일대다 관계에 있는 개체의 속성을 count한 것을 정렬의 기준으로 삼는 것이 prisma ORM에서는 지원하지 않는다는 것이 문제였다.
(relation count)

하지만, 강사님 조언으로 다른 테이블의 데이터를 가져와서 다른 테이블에서 저장하는 것은 데이터 관리의 안정성이 깨질 수 있는 위험이 있어서 지양하도록 하고 있어서 다른 방법이 필요했다.
(기사님 리뷰 건수를 기사 프로필 테이블에 저장하는 것은 좋지 않다. 수정이 동시에 두 곳에서 일어나야 하고, 모든 api가 그것을 보장하지 못할 수 때문이다. => 모든 동업 개발자에게 그 사항을 따르게 하는 것이 어려울 수 있다.)

그래서 추천 받은 내용은 뷰였다.

## 일반 view VS materialized view

| 일반 view                                       | materialized view                                 |
| --------------------------------------------- | ------------------------------------------------- |
| sql 문으로 작성된 쿼리를 뷰 열람 시 마다 새로 수행.<br>(쿼리를 저장)v | 물리적인 데이터를 가지는 뷰.<br>외부에서 뷰를 갱신해주는 코드를 실행할 필요가 있다. |
 예시 코드 
 ```postgresql
CREATE MATERIALIZED VIEW "DriverStats" AS
SELECT
u.id AS "driverId",
u.name,
dp.career,
NULLIF(regexp_replace(dp.career, '\D', '', 'g'), '')::int AS "careerNumber",
dp."imageUrl" AS "imageUrl",
dp."shortIntro" AS "shortIntro",
COALESCE(stat.review_count, 0) AS "reviewCount",
COALESCE(stat.avg_rating, 0) AS "avgRating",
COALESCE(stat.confirmed_count, 0) AS "confirmedCount",
u."createdAt"
FROM "User" u
JOIN "DriverProfile" dp ON u.id = dp."driverId"
LEFT JOIN (
SELECT
e."driverId",
COUNT(r.id) AS review_count,
AVG(r.rating) AS avg_rating,
COUNT(CASE WHEN e.status = 'CONFIRMED' THEN 1 END) AS confirmed_count
FROM "Estimate" e
LEFT JOIN "Review" r ON e.id = r."estimateId"
GROUP BY e."driverId"
) stat ON u.id = stat."driverId"
WHERE u.type = 'DRIVER' AND u."isDelete" = false;
 ```

정렬 기준으로 삼을 데이터를 뽑아낸 뷰를 가지게 된다.
![[Pasted image 20260102180818.png]]
## materialized view 갱신 테크닉
node_cron

node.js에서 사용하기 좋은 cron 라이브러리

정기적으로 뷰를 갱신하는 sql문을 실행하는 용도로 사용.

```js
const cron = require('node-cron');

// 매일 새벽 3시에 실행 (CRON 표현식: 초 분 시 일 월 요일)
cron.schedule('0 3 * * *', async () => {
  try {
    console.log('Refreshing materialized view...');
    await client.query('REFRESH MATERIALIZED VIEW driver_stats;');
    console.log('Materialized view refreshed successfully!');
  } catch (err) {
    console.error('Error refreshing materialized view:', err);
  }
});

```

## prisma ORM에서 이용하기

뷰는 prisma 에서 생성이 불가능해서 dbeaver와 같은 툴에서 sql 문법을 통해 (정확히는 postresql) 만들게 되지만, 사용하는 것은 prisma에서 등록을 통해 가능하다.

### 📌 Prisma에서 View 스키마 추가 방법

1. Preview 기능 활성화

`schema.prisma` 파일의 `generator client` 블록에 다음을 추가합니다:

```js
generator client { provider = "prisma-client-js" previewFeatures = ["views"] }
```

- `views`는 아직 **Preview 기능**이므로 반드시 활성화해야 합니다.

2. 데이터베이스에서 View 생성

Prisma는 View 자체를 생성하지 않으므로, **DB에서 직접 SQL로 View를 만들어야 합니다**.  
예: MySQL에서 `UserInfo` 뷰 생성

```postgresql
CREATE VIEW UserInfo AS SELECT id, email, name, bio FROM User;
```

3. Prisma 스키마에 View 정의

DB에 만든 View를 `schema.prisma`에 추가합니다:

```js
view UserInfo { id Int @unique email String name String bio String }
```

- `view` 키워드를 사용합니다.
- 일반 `model`과 유사하게 정의하지만, **View는 읽기 전용**입니다.