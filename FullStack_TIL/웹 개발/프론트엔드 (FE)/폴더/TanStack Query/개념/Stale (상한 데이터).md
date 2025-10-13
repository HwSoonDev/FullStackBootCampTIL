Stale이란,
=데이터가 상했는가
![[Pasted image 20250923144433.png]]
원격에서 가져오는 데이터는 캐시로 보관하여, 변화가 없다면 다시 받아오는 대신에 캐시된 데이터를 사용한다.

너무 오래된 데이터는 상한 데이터로 간주하여 다시 받아오게 하는데, 이 시간을 설정할 수 있다. (데이터의 유통기한을 설정 가능)

상하지 않은 상태를 Fresh,
상한 상태를 Stale이라고 한다.

![[Pasted image 20250923144513.png]]

상하는 시간 설정 방법 3가지

1. **개별 쿼리에서 설정**
```jsx
useQuery({ queryKey: ['users'], queryFn: fetchUsers, staleTime: 1000 * 60 * 5, // 5분 });
```

2. **특정 queryKey에 기본값 설정**
```jsx
const queryClient = useQueryClient(); queryClient.setQueryDefaults(['users'], { staleTime: 1000 * 60 * 5, });
```

3. **전역 설정 (QueryClient 생성 시)**
```jsx
const queryClient = new QueryClient({ defaultOptions: { queries: { staleTime: 1000 * 60 * 5, // 모든 쿼리에 적용 }, }, });
```
