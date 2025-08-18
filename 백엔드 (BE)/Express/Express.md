
## Exress

![[Pasted image 20250816110634.png]]

## Express 뼈대 코드

```js
import express from 'express';

const app = express();

// 라우트 정의

app.listen(3000, () => console.log('Server Started'));
```

## 라우트(Route) 정의

Express 라우트의 정의

```js
app.method(path, handler)
```

- `method`: HTTP 메소드 이름 (get, post, patch...)이 들어간다.
- `path`: 엔드포인트 경로
- `handler`: 리퀘스트 로직을 처리하고 리스폰스를 돌려주는 핸들러 함수. 첫 번째 파라미터로 리퀘스트 객체, 두 번째 파라미터로 리스폰스 객체를 받습니다.


## mock 데이터 보내기

mock.js 안에 data라는 js 객체를 만들고
send로 보내면 express가 자동으로 데이터를 감지해서 적절한 타입으로 보내게 된다.
예시에서는 객테를 json으로 자동으로 바꿔 보내준 모습이다.

![[Pasted image 20250816115606.png]]
GET 리퀘스트 Content-Type을 보면 json으로 되어 있다.

mock 데이터는 서버 실행 시 정해지기 초기화되기 때문에,
따로 DB 사용없이 mock으로 구현하면 리퀘스트로 값을 바꿔도 저장이 되지 않는다. 
## 엔드 포인트

HTTP 메소드와 URL로 구성된 엔드포인트(내가 리퀘스트라고 부르는 것들) (GET, POST...)
들의 모음을 API라고 부른다.

![[Pasted image 20250816110213.png]]
![[Pasted image 20250816110355.png]]


## mock 데이터 보내기

mock.js 안에 data라는 js 객체를 만들고
send로 보내면 express가 자동으로 데이터를 감지해서 적절한 타입으로 보내게 된다.
예시에서는 객테를 json으로 자동으로 바꿔 보내준 모습이다.

![[Pasted image 20250816115606.png]]
GET 리퀘스트 Content-Type을 보면 json으로 되어 있다.

mock 데이터는 서버 실행 시 정해지기 초기화되기 때문에,
따로 DB 사용없이 mock으로 구현하면 리퀘스트로 값을 바꿔도 저장이 되지 않는다. 

