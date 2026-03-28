웹 소켓은 SSE와는 조금 다르다.

웹소켓 통신은 양방향 통신이다.

클라이언트가 서버에 HTTP 요청을 통해 웹소켓 통신을 시작하자고 알리면,
서버가 승낙하고 웹소켓 통신을 시작한다.

## 시작하기 (ws 라이브러리 기준)

```shell
npm init
npm install express // express 웹서버를 만들기
npm install ws // 웹소켓
```

#### 프론트엔드 

html에 직접 넣어 줄 때는 웹소켓 백엔드 주소만 적어서 정의해주면 된다.

```html
<! DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<title>Document</title>
	</head>
	<body>
		<h4>채팅페이지입니다</h4>
		<button id="send" onCLick="socket.send('ㅎㅇ')">메세지 보내기</button>
		
		<script>
			let socket = new WebSocket("ws://localhost:8081");
		</script>
	</body>
</html>
```


### 백엔드

```js
const app = express();

app.use("/", function(req, res){
	res.sendFile(_dirname + '/index.html');
});

app.listen(8080);

const WebSocket = require('ws');

const socket = new WebSocket.Server({
	port: 8081
});

socket.on('connection', (ws, req)=>{
	ws.on('message', (msg)=>{
		console.log('유저가 보낸거 : ' + msg);
	})
});
```