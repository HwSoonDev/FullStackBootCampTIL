### 상태 저장

```JSX
const [items, setItems] = useState([]);
```
상태를 저장 하는 훅
상태의 변화가 감지되면 컴포넌트가 재렌더링 된다.

콜백으로 초기값 지정하기
```JSX
const [state, setState] = useState(() => { 
	// 초기값을 계산 
	return initialState; 
});
```
계산식이 들어갈 때 사용한다.
