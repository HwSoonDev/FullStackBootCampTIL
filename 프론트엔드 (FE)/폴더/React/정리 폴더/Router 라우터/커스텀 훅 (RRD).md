
# RRD 커스텀 훅

RRD (React Router Dom)

리액트 라우터에서 지원하는 커스텀 훅

## 1. useParams

주소 창의 추가 경로를 변수로 저장해서 컴포넌트에 사용할 수 있게 해주는 커스텀 훅이다.

먼저 Route에서 변수 이름 지정이 필요하다.
지정한 변수가 하위 경로를 대변해준다.
```
<Route path="courses">
	<Route index element={<CourseListPage />} />
	<Route path=":courseSlug" element={<CoursePage />} />
</Route>
```
#### 리액트 파라미터 
":courseSlug"처럼 ( : )콜론으로 path에 적어주면 cousreSlug라는 **변수에 경로를 입력**해줄 수 있다.
이걸 리액트 파라미터라고 부른다.

```JSX
function CorsePage(){
	const {couseSlug} = useParams();
	const course = getCouseBySlug(couresSlug);
	//... 
}
```

그러면 **해당 경로가** useParams()안에 **변수로 저장**되고, **접근할 수 있게** 된다. 

이렇게 설정하면 경로마다 Route를 만들어야 하는 번거로움을 해결할 수 있다. -> **동적 경로**
(컴포넌트 내에서만 useParams를 받아서 잘 처리해주면 된다.)


## 2. useSearchParams

쿼리 값을 가져오는 리액트 라우터의 커스텀 훅.
`searchParams`와 세터 함수를 배열형으로 리턴

##### 1. 쿼리를 가져오려면 `searchParams`의 get()함수를 사용한다.

예시) 쿼리 파라미터 keyword 가져오기
```JSX
const [searchParams, setSearchParams] = useSearchParams(); 
const initKeyword = searchParams.get('keyword');
```

##### 2. 주소 창에 쿼리를 넣을 때는 세터 함수 `setSearchParams()`에 객체를 전달

```JSX
setSearchParams({ keyword: 'react' });
//주소창의 쿼리 값이 `?keyword=react`로 변경
```
주소창의 쿼리 값이 `?keyword=react`로 변경된다.

1) **쿼리 상태 저장**
2) **주소창 쿼리 변경** 
3) **주소창 쿼리 받아오기**

순으로 사용한다.

#### 예제 - useSearchParam

1) 검색 input 값이 바뀌면 keyword 상태(State) 값을 변경 **`handleKeywordChange`**
2) Submit 핸들러 감지 시,  keyword 상태 값이 있다면 searchParam 에 객체 { keyword } 전달 **`handleSubmit`** -> 주소창 검색 쿼리가 변경된다.
3) 다시 주소창의 쿼리 값을 initKeyword에 저장.  **`searchParams.get('keyword')`**
4) **`quetions`**(검색 결과)를 **`getQuestions(initKeyword)`** 로 받아온다. (예제에서는 GET 리퀘스트가 아니라 mock파일로 단순 구현했다.)

```JSX
function QuestionListPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  const initKeyword = searchParams.get('keyword');
  
  const [keyword, setKeyword] = useState(initKeyword || '');
  const questions = getQuestions(initKeyword);
  
  
  const handleKeywordChange = (e) => setKeyword(e.target.value);
  
  const handleSubmit = (e) => {
    e.preventDefault(); //Submit 페이지 이동 방지
    
    //setSearchParams는 객체를 파라미터로 넘긴다.
    setSearchParams(keyword ? { keyword } : {}); //keyword가 없으면 빈 객체 넘김.
  }

  return (
    <ListPage
      variant="community"
      title="커뮤니티"
      description="코드댓의 2만 수강생들과 함께 공부해봐요."
    >
      <form className={searchBarStyles.form} onSubmit={handleSubmit}>
        <input
          name="keyword"
          value={keyword}
          placeholder="검색으로 질문 찾기"
          onChange={handleKeywordChange}
        />
        <button type="submit">
          <img src={searchIcon} alt="검색" />
        </button>
      </form>

      <p className={styles.count}>총 {questions.length}개 질문</p>

      {initKeyword && questions.length === 0 ? (
        <Warn
          className={styles.emptyList}
          title="조건에 맞는 질문이 없어요."
          description="올바른 검색어가 맞는지 다시 한 번 확인해 주세요."
        />
      ) : (
        <div className={styles.questionList}>
          {questions.map((question) => (
            <QuestionItem key={question.id} question={question} />
          ))}
        </div>
      )}
    </ListPage>
  );
}
```


## 3. useNavigate

함수 내에서 주소 이동을 해주는 커스텀 훅

컴포넌트 반환 시에는 
```JSX
<Navigate to=""/>
```
를 반환해주면 주소 이동이 가능하지만,

함수 내에서 주소 이동이 하고 싶다면,
useNavigate로 만든 함수를 사용 할 수 있다.
```JSX
function a(){
	const navigate = useNavigate();
	const handleWishListClick = () => {
		addWishlist(course?.slug)
		navigate('/wishlist');
	} 
}
```
