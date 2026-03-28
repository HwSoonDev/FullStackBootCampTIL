ws 방식은 원초적인 기술이고, 웹소켓을 좀 더 원활하게 사용하기 위한 라이브러리는 sokect.io다.

(웹소켓 통신은 자바스크립트 외에도 다양한 언어와 플랫폼에서 지원해주고 있고, socket.io도 마찬가지다. 그래서 이번에 다루는 것은 Node.js 기반 자바스크립트에서의 socket.io다.)

## 기초 

`socket`을 사용하기 위해서 먼저 `io`를 통해서 선언해야 한다.
```js
const socket = io({
  retries: 3,
  ackTimeout: 10000
});
```

```js
const socket = io({
  auth: {
    offset: undefined
  }
});

socket.on("my-event", ({ id, data }) => {
  // do something with the data, and then update the offset
  socket.auth.offset = id;
});
```

보내는 것은 `socket.emit()`
받는 것은 `socket.on()` 이다.

보낼 때 (Sender)
```js
socket.emit("hello", "world", (response) => {
  console.log(response); // "got it" 보낸 내용에 대한 답변
});
```

받을 때 (Receiver)
```js
socket.on("hello", (arg, callback) => {
  console.log(arg); // "world" 첫번째 인자 (arg)는 받은 내용.
  callback("got it"); //callback은 답장하는 함수
});
```

타임아웃
```js
socket.timeout(5000).emit("hello", "world", (err, response) => {
  if (err) {
    // the other side did not acknowledge the event in the given delay
  } else {
    console.log(response); // "got it"
  }
});
```


```js
io.on("connection", (socket) => {
  socket.emit("noArg");
  socket.emit("basicEmit", 1, "2", Buffer.from([3]));
  socket.emit("withAck", "4", (e) => {
    // e is inferred as number
  });

  // works when broadcast to all
  io.emit("noArg");

  // works when broadcasting to a room
  io.to("room1").emit("basicEmit", 1, "2", Buffer.from([3]));
});
```
## 예시
### 프론트엔드

```html
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io();

  var form = document.getElementById('form');
  var input = document.getElementById('input');

  form.addEventListener('submit', function(e) {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });
</script>
```

html에 js를 바로 적용하는 예시다.

리액트 방식으로 적용해보면
form.addEventListener 이벤트 리스너 대신에

const  handleSubmit () 화살표 함수를 만들어서 form 태그의 onSubmit 인자로 넣어줬을 것이다.

### 백엔드

```js
io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    console.log('message: ' + msg);
  });
});
```