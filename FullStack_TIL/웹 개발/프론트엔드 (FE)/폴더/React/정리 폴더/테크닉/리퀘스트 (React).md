
```JSX
function App() {
  const [order, setOrder] = useState('createdAt');
  const [items, setItems] = useState([]);

  const handleNewestClick = () => setOrder('createdAt');

  const handleCalorieClick = () => setOrder('calorie');

  const handleDelete = (id) => {
    const nextItems = items.filter((item) => item.id !== id);
    setItems(nextItems);
  };

  const handleLoad = async () => {
    const { foods } = await getFoods();
    setItems(foods);
  };
  
  handleLoad();

  const sortedItems = items.sort((a, b) => b[order] - a[order]);

  return (
    <div>
      <button onClick={handleNewestClick}>최신순</button>
      <button onClick={handleCalorieClick}>칼로리순</button>
      <FoodList items={sortedItems} onDelete={handleDelete} />
    </div>
  );
}
```

```JSX
export async function getFoods() {
  const response = await fetch('https://learn.codeit.kr/api/foods');
  const body = await response.json();
  return body;
}
```

예시는 GET 리퀘스트.
그런데, handleLoad()를 실행하면서 State 값이 바뀌어서
App() 컴포넌트를 통째로 다시 실행하게 되고(재렌더링) 무한 루프에 빠지는 현상이 생긴다.

페이지가 로드 될 때 리퀘스트를 보내는 건 좋지만, 계속 보내는 문제가 있다.
이 때 useEffect() 훅 안에 넣으면 해결된다.

한번만 상태를 바꾸도록 허락하는 훅이다.

```JSX
import { useState, useEffect } from 'react';

useEffect(() => {
	handleLoad();  
}, []);
```

