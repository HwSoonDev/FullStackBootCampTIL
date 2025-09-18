
## MongoDB 사용하는 방법 두가지
1. 로컬에 MongoDB 설치:  -> 배포시 서버에도 새로 설치가 필요하다. (오류 발생 가능성 있음)
2. MongoDB Atlas 이용. (클라우드 원격 DB)

(실습에서는 클라우드 DB (Atlas) 사용)

## Atlas
[[MongoDB Atlas 사용하기]]
`env.js`라는 파일에 원격 DB URL을 복사 붙여넣기
```js
export const DATABASE_URL =
'mongodb+srv://<username>:<db_password>@cluster0.tqjetm0.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0';

```

`<username>` 부분에 사이트 계정 이름을 쓰고,
`<db_password>` 부분에 db 비밀번호 적기

## Mongoose 설치

```bash
npm i mongo@"<8.0.0"
```
실습에선 7버전을 사용한다.

app.js에 다음과 같이 작성.

```js
import mongoose from 'mongoose';
import { DATABASE_URL } from './env.js';

// ...

mongoose.connect(DATABASE_URL).then(() => console.log('Connected to DB'));

```