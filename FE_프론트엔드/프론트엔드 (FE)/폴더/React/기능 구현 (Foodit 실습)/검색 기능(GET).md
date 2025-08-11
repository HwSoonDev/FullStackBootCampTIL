sumbit 버튼을 눌렀을 때 Input값이 상태(State)로 저장되고,
검색 쿼리로 들어간다.

예시에서는 order나 search가 바뀌면 useEffect()가 실행된다.
(다시 리스트가 전체 로드된다.)

```JSX
const [search, setSearch] = useState('');

const handleLoadMore = () => {
    handleLoad({
      order,
      cursor,
      search,
    });
  };
  
const handleSearchSubmit = (e) => {
	e.preventDefault();
	setSearch(e.target['search'].value);
};

useEffect(() => {
    handleLoad({
      order,
      search,
    });
  }, [order, search]);
```

```JSX
<form onSubmit={handleSearchSubmit}>
	<input name="search" />
	<button type="submit">검색</button>
</form>
```

```JSX
export async function getFoods({
  order = '',
  cursor = '',
  limit = 10,
  search = '',
}) {
  const query = `order=${order}&cursor=${cursor}&limit=${limit}&search=${search}`;
  const response = await fetch(`https://learn.codeit.kr/api/foods?${query}`);
  if (!response.ok) {
    throw new Error('데이터를 불러오는데 실패했습니다');
  }
  const body = await response.json();
  return body;
}
```