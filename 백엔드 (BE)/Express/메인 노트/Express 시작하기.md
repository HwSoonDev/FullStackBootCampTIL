### 1. Express 패키지 설치

```bash
npm install express
npm install --save-dev nodemon
```

`nodemon`은 코드를 수정했을 때 서버를 즉시 재시작해 주는 툴.

![[Pasted image 20250816111653.png]]

dependency에 `nodemon`이 잘 추가가 되었는지 확인.

### 2. vscode 익스텐션에서 `rest client` 설치

vscode 내에서 api 리퀘스트를 보내서 api 가 잘 작동하는 지 확인하는 툴.
![[Pasted image 20250816111827.png]]

### 3. type 필드 추가

```json
{ 
	"dependencies": { ... }, 
	"devDependencies": { ... }, 
	"type": "module" 
}
```

### 4. 서버 실행하는 법

원래는 이렇게 node에서 파일을 실행한다.
```bash
node app.js
```

실행 스크립트를 설정하고 나면,
```json
{
  "dependencies": {
    "express": "^5.1.0"
  },
  "devDependencies": {
    "nodemon": "^3.1.10"
  },
  "type" : "module",
  "scripts" : {
    "dev" : "nodemon app.js",
    "start" : "node app.js"
  }
}
```

```bash
npm run dev
npm run start
```
npm run으로 실행 할 수 있다.
dev는 테스트용으로 nodemon에서 실행 -> 테스트
start는 배포용으로 node에서 그냥 실행.

### 5. `rest client`로 리퀘스트 보내기

```http
GET http://localhost:3000/hello
```

`request.http` 라는 파일을 프로젝트 폴더 내에 생성해서 
안에 리퀘스트를 작성해주면,
해당 코드 위에 `send request`라는 버튼이 생기는 데 그걸 눌러주면 리퀘스트 시험을 할 수 있다.

![[Pasted image 20250816114421.png]]

간단히 구현해본 GET 리퀘스트 서버.
```js
import express from 'express';

const app =express();

//GET 리퀘스트가 왔을 떄 처리할 내용.
// 두번 째 아규먼트에는 함수를 넣는데, 예시처럼 애로우 함수를 넣도 된다.

app.get('/hello', (req, res) => {
    res.send('Hello Express!!');
});

//3000토드에서 서버 실행.
//실행 시 문구만 보여서 정확히 어디서 어떻게 작동되고 수정되고 있는지 확인이 어려움...
app.listen(3000, () => console.log('Server Started'));
```