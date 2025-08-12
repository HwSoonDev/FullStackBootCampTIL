React는 html요소를 React객체라는 것으로 만들 수 있다.

1. 변수에 저장
2. 함수 리턴 값으로 반환   

등으로 사용할 수 있는데,  

1) React 객체를 반환하는 함수를 만든 후
2) JSX식 html 코드 안에서
3) html 태그처럼 호출

하는 것을 "컴포넌트"라고 한다.

**컴포넌트 = React HTML의 "부품"**


```JSX
import ReactDOM from 'react-dom/client';
const root = ReactDOM.createRoot(document.getElementById('root'));

function Hello(){
	return <h1>안녕 리액트!</h1>;
}

const element = {
	<>
		<Hello />
		<Hello />
		<Hello />
	</>
}

root.render(
	element
);
```
