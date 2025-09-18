
컴포넌트의 재렌더링 때마다 새로 만들어지는 함수를
의존성 배열 (Dependency List) 에 넣으면
무한 반복에 빠질 수 있다.

의존성 배열의 함수가 변경되었을 때만 함수를 생성하도록 하고 싶을 때,
useCallback을 사용한다.

```JSX
import { useEffect, useState } from "react"; 

function App() { 
	const [count, setCount] = useState(0); 
	const [num, setNum] = useState(0); 
	const addCount = () => { 
		setCount((c) => c + 1); 
		console.log(`num: ${num}`); 
	}; 
	const addNum = () => setNum((n) => n + 1); 
	useEffect(() => { 
		console.log('timer start'); 
		const timerId = setInterval(() => { 
			addCount(); 
		}, 1000); 
		return () => { 
			clearInterval(timerId); 
			console.log('timer end'); 
		}; 
	}, [addCount]); 
	return ( 
		<div> 
			<button onClick={addCount}>count: {count}</button> 
			<button onClick={addNum}>num: {num}</button> 
		</div> 
	); 
} 

export default App;

```