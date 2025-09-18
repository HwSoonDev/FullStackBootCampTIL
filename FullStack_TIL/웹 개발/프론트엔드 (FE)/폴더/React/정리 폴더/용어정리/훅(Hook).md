**"훅(Hook)"** 은 리액트(React)에서 **함수형 컴포넌트(function component)** 에서 **상태(state)** 나 **생명주기(lifecycle)** 같은 기능을 사용할 수 있게 해주는 **특별한 함수**예요.

### 📌 왜 필요한가요?

예전엔 이런 기능을 **클래스형 컴포넌트**에서만 사용할 수 있었어요.  
하지만 리액트가 함수형 컴포넌트를 더 선호하면서, 그런 기능을 **훅**으로 제공하게 된 거예요.

---

### 🧩 자주 쓰는 대표적인 훅들

|훅 이름|설명|
|---|---|
|`useState`|컴포넌트에 상태(state)를 추가함|
|`useEffect`|컴포넌트가 마운트되거나 업데이트될 때 특정 작업 수행 (ex. API 호출)|
|`useRef`|DOM에 직접 접근하거나, 값 저장할 때|
|`useContext`|전역 상태 관리 (Context API 사용)|
|`useMemo` / `useCallback`|성능 최적화용 (계산 결과, 함수 재사용)|

---

### ✅ 간단한 예: `useState`

```JSX
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>현재 숫자: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

👉 여기서 `useState(0)`는 상태값 `count`를 0으로 초기화하고,  
`setCount`로 값을 바꿀 수 있게 해줘요.