JSX에서는 index.html 의 root태그로 설정한 태그 아래에 리액트 객체를 삽입하는 방식으로
DOM을 렌더링한다.

```JSX
import ReactDOM from 'react-dom/client';
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<h1>안녕 리액트!</h1>);
```

ReactDOM.createRoot()로 태그를 루트로 만든 후, (HTML에 집어 넣기 할 태그 부분)
root.render() 안에 html을 작성하는 것이 현재 일반적인 방법.

```html
<!doctype html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>주사위 게임</title>
  </head>
  <body>
    <div id="root"> 여기 안에 들어간다. </div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```
