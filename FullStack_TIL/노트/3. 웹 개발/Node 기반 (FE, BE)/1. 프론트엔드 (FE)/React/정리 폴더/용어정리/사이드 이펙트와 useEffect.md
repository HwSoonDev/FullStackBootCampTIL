
`useEffect` 는 리액트 컴포넌트 함수 안에서
**사이드 이펙트를 실행하고 싶을 때** 사용하는 함수입니다.

예를들면 DOM 노드를 직접 변경한다거나,
브라우저에 데이터를 저장하고,
네트워크 리퀘스트를 보내는 것처럼 말이죠.

주로 **리액트 외부에 있는 데이터나 상태를 변경할 때** 사용하는데요.
간단한 예시들을 보면서 어떤 식으로 활용할 수 있는지 가볍게 살펴봅시다.

1)  페이지 정보 변경
```JSX
useEffect(() => { document.title = title; // 페이지 데이터를 변경 }, [title]);
```
2) 네트워크 요청
```JSX
useEffect(() => { 
	fetch('https://example.com/data') // 외부로 네트워크 리퀘스트 
	.then((response) => response.json()) 
	.then((body) => setData(body)); 
}, [])
```
3) 데이터 저장
```JSX
useEffect(() => { 
	localStorage.setItem('theme', theme); // 로컬 스토리지에 테마 정보를 저장 
}, [theme]);
```
4) 타이머
```JSX
useEffect(() => { 
	const timerId = setInterval(() => { 
		setSecond((prevSecond) => prevSecond + 1); 
	}, 1000); // 1초마다 콜백 함수를 실행하는 타이머 시작 
	
	return () => { clearInterval(timerId); } 
}, []);
```

# useEffect를 쓰면 좋은 경우

### 1) 핸들러 함수만 쓴 경우

```JSX
import { useState } from 'react'; 
const INITIAL_TITLE = 'Untitled'; 

function App() { 

	const [title, setTitle] = useState(INITIAL_TITLE); 
	
	const handleChange = (e) => { 
		const nextTitle = e.target.value; 
		setTitle(nextTitle); 
		document.title = nextTitle; 
	}; 
	const handleClearClick = () => { 
		const nextTitle = INITIAL_TITLE; setTitle(nextTitle); 
		document.title = nextTitle; 
	}; 
	
	return ( 
		<div> 
			<input value={title} onChange={handleChange} /> 
			<button onClick={handleClearClick}>초기화</button> 
		</div> 
	); 
} 

export default App;
```

### 2) useEffect()를 같이 쓴 경우

```JSX
import { useEffect, useState } from 'react'; 
const INITIAL_TITLE = 'Untitled'; 

function App() { 
	
	const [title, setTitle] = useState(INITIAL_TITLE);
	
	const handleChange = (e) => { 
		const nextTitle = e.target.value; setTitle(nextTitle); 
	}; 
	
	const handleClearClick = () => { 
		setTitle(INITIAL_TITLE); 
	}; 
	
	useEffect(() => { 
		document.title = title; 
	}, [title]); 
	
	return ( 
		<div> 
			<input value={title} onChange={handleChange} /> 
			<button onClick={handleClearClick}>초기화</button> 
		</div> 
	); 
} 

export default App;
```

