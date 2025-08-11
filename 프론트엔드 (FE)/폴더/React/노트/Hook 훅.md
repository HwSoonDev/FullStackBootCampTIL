리액트(React)에서 **함수형 컴포넌트(function component)** 에서 **상태(state)** 나 **생명주기(lifecycle)** 같은 기능을 사용할 수 있게 해주는 **특별한 함수**

예전엔 이런 기능을 **클래스형 컴포넌트**에서만 사용할 수 있었다.  
하지만 리액트가 함수형 컴포넌트를 더 선호하면서, 그런 기능을 **훅**으로 제공하게 되었다.

1. [useState()](https://github.com/pondsharky/FullStackBootCampTIL/blob/main/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%20(FE)/%ED%8F%B4%EB%8D%94/React/Hook/useState().md) - 상태 저장
2. [useEffect()](https://github.com/pondsharky/FullStackBootCampTIL/blob/main/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%20(FE)/%ED%8F%B4%EB%8D%94/React/Hook/useEffect().md) - 한번 실행, 의존성 배열, 사이드 이펙트
3. [useRef()](https://github.com/pondsharky/FullStackBootCampTIL/blob/main/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%20(FE)/%ED%8F%B4%EB%8D%94/React/Hook/useRef().md) - DOM 노드 참조
4. [useContext()](https://github.com/pondsharky/FullStackBootCampTIL/blob/main/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%20(FE)/%ED%8F%B4%EB%8D%94/React/Hook/useContext().md) - 전역 상태 관리 
5. [useCallback() & useMemo()](https://github.com/pondsharky/FullStackBootCampTIL/blob/main/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%20(FE)/%ED%8F%B4%EB%8D%94/React/Hook/useCallback()%20%26%20useMemo().md) - 함수 재사용 /  값 재사용 -> 컴포넌트 재렌더링 시에도 한번 생성 후 생성 안됨

---

# Hook의 규칙

- 반드시 리액트 컴포넌트 함수(Functional Component) 안에서 사용해야 함
- 컴포넌트 함수의 최상위에서만 사용 (조건문, 반복문 안에서 못 씀)
- Hook은 선언된 순서대로 저장되기 때문에 순서가 바뀌면 안된다. (-> 분기, 반복이 안되는 이유)


![[Pasted image 20250807092351.png]]


| 훅 이름                      | 설명                                         |
| ------------------------- | ------------------------------------------ |
| `useState`                | 컴포넌트에 상태(state)를 추가함                       |
| `useEffect`               | 컴포넌트가 마운트되거나 업데이트될 때 특정 작업 수행 (ex. API 호출) |
| `useRef`                  | DOM에 직접 접근하거나, 값 저장할 때                     |
| `useContext`              | 전역 상태 관리 (Context API 사용)                  |
| `useMemo` / `useCallback` | 성능 최적화용 (계산 결과, 함수 재사용)                    |


