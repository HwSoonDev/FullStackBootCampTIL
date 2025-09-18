**컴포넌트가 생성, 업데이트, 제거되는 일련의 과정**을 의미합니다. 이 생명주기를 이해하면 상태(state), 속성(props), DOM 처리 등에서 **정확한 타이밍에 코드 실행**이 가능해집니다.

React의 생명주기는 클래스형 컴포넌트와 함수형 컴포넌트에서 조금 다르게 적용됩니다.

---

## 1. 함수형 컴포넌트의 생명주기 (React Hooks 기준)

함수형 컴포넌트에서는 `useEffect`, `useLayoutEffect` 등의 Hook을 사용해 생명주기를 제어합니다.

### 생명주기 흐름 (메서드)

| 생명주기 단계            | 메서드 이름                             | 설명                          |
| ------------------ | ---------------------------------- | --------------------------- |
| **마운트 (Mount)**    | **`useEffect(() => {}, [])`**      | 컴포넌트가 DOM에 처음 렌더링           |
| **업데이트 (Update)**  | **`useEffect(() => {}, [state])`** | 상태(state)나 props가 변경되어 재렌더링 |
| **언마운트 (Unmount)** | **`useEffect`**의 return 함수         | 컴포넌트가 DOM에서 제거              |

### 📌 예시 코드:

```jsx
import { useEffect, useState } from 'react';

function MyComponent() {
  const [count, setCount] = useState(0);

  // 마운트 시 실행 (한 번만)
  useEffect(() => {
    console.log('컴포넌트가 마운트됨');

    // 언마운트 시 실행
    return () => {
      console.log('컴포넌트가 언마운트됨');
    };
  }, []);

  // count가 변경될 때마다 실행 (업데이트)
  useEffect(() => {
    console.log('count가 변경됨:', count);
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>Click</button>;
}
```

---

## 2. 클래스형 컴포넌트의 생명주기

### 생명주기 흐름 (메서드)

| 단계       | 메서드 이름                              | 설명                     |
| -------- | ----------------------------------- | ---------------------- |
| **마운트**  | `constructor()`                     | 초기화 (state 설정 등)       |
|          | `static getDerivedStateFromProps()` | props로부터 state 동기화     |
|          | `render()`                          | JSX 렌더링                |
|          | **`componentDidMount()`**           | DOM 생성 이후, 초기 API 호출 등 |
| **업데이트** | `static getDerivedStateFromProps()` | props 변경 시 호출됨         |
|          | `shouldComponentUpdate()`           | 렌더링 여부 결정 (성능 최적화)     |
|          | `render()`                          | 변경된 내용 렌더링             |
|          | `getSnapshotBeforeUpdate()`         | 업데이트 직전 DOM 상태 저장      |
|          | **`componentDidUpdate()`**          | 업데이트 후 작업 (API 호출 등)   |
| **언마운트** | **`componentWillUnmount()`**        | 정리 작업 (타이머, 구독 해제 등)   |

---

## ✅ 마운트 / 업데이트 / 언마운트 간단 요약 (비교표)

|단계|함수형 (Hook)|클래스형|
|---|---|---|
|마운트|`useEffect(() => {}, [])`|`componentDidMount()`|
|업데이트|`useEffect(() => {}, [deps])`|`componentDidUpdate()`|
|언마운트|`useEffect`의 `return` 함수|`componentWillUnmount()`|

## ✅ 예시 용도별 정리

|목적|추천 Hook / 메서드|
|---|---|
|API 호출 (초기)|`useEffect(() => {}, [])` or `componentDidMount()`|
|이벤트 리스너 등록 / 해제|`useEffect`의 return 활용|
|DOM 접근 전 처리|`useLayoutEffect()` (Hook 전용)|
|렌더링 여부 제어|`shouldComponentUpdate()` (클래스 전용)|
