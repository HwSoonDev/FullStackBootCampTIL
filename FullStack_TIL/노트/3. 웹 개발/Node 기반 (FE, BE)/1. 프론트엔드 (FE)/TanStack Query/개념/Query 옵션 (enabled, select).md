
## `enabled` 옵션

- **역할**: 쿼리 실행 여부를 제어
- **기본값**: `true` (즉시 실행)

```jsx
const { data } = useQuery({
  queryKey: ['user', userId],
  queryFn: fetchUser,
  enabled: !!userId, // userId가 있을 때만 실행
});
```

- 특정 조건이 충족될 때만 쿼리를 실행하고 싶을 때
- 예: `userId`가 아직 없거나, 버튼 클릭 후 실행하고 싶을 때

## `select` 옵션

- **역할**: 쿼리 결과를 가공해서 반환.
- **사용 예시**:

```jsx
const { data, isLoading } = useQuery({
  queryKey: ['posts', userId],
  queryFn: () => fetchPosts(userId),
  enabled: !!userId,
  select: (posts) => posts.filter(post => post.published),
});
```

- 컴포넌트에서 필요한 데이터만 추출 가능
- memoization이 적용되어 성능 향상
- `data`는 `select`의 반환값이 됨