예시)
```js
function TodoList() {

	// 아래 세 개의 state를 모두 수동 관리해야 한다는 점!! ☠️
	// 그렇다고 안 할수도 없고
	const [todos, setTodos] = useState([]);
	const [isLoading, setIsLoading] = useState(true);
	const [error, setError] = useState(null);
	
	useEffect(() => {
		const getTodos = async () => {
			try {
				const data = await fetch(`${API_URL}/todos`).then(res => res.json());
				setTodos(data);
			}
			catch (error) {
				setError(error)
			}
		  finally () {
			  setIsLoading(false);
		  }
		}
		getTodos();
	}, []);
	
	if (isLoading) return <p>로딩중...</p>
	if (error) return <p>error: {error}</p>
	
	return ... // 투두리스트 UI
}
```