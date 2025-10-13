마찬가지로 
1) pending
2) error
3) 캐시 기능
을 제공한다.

api를 불러옴과 동시에 캐시를 수정해주는 데,

api로 값을 받아온다고 해도 stale time 이내라면 refresh가 일어나지 않기 때문에, 
(캐시 값이 바뀌지 않기 때문에) UI에 변화가 생기지 않는다.

```
refresh (새로고침) : 
	=> TanStack Query의 특징 최신 데이터가 컴포넌트에 영향을 주는 기준
```


사용 예시) - 이 코드 만으로는 UI(컴포넌트)에 변화가 없다.
```jsx
const uploadPostMutation = useMutation({
  mutationFn: (newPost) => uploadPost(newPost),
});

const handleSubmit = (e) => {
  e.preventDefault();
  const newPost = { username: 'codeit', content };
  uploadPostMutation.mutate(newPost);
  setContent('');
};
```

### invalidateQuries() 함수 & onSuccess 콜백 함수
: 성공 시 자동 refresh 하는 로직 짜기

이때 refresh를 해결해주는 함수가 `invalidateQueries()` 함수다.
캐시에 있는 데이터가 stale time 이 지났는지 상관없이, stale 상태로 만든다.
stale상태가 된 데이터는 자동으로 refresh되며 UI에 변화를 준다.

onSuccess는 useMutation에 들어가는 인자 중 하나로, mutate 성공 시 실행할 콜백 함수를 받는다. 여기에 `invalidateQueries()`를 넣어 주면 api를 실행한 결과를 바로 캐시에 반영하고, UI에 변화가 생긴다.

```jsx
const queryClient = useQueryClient();

// ...

const uploadPostMutation = useMutation({
  mutationFn: (newPost) => uploadPost(newPost),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['posts'] });
  },
});
```