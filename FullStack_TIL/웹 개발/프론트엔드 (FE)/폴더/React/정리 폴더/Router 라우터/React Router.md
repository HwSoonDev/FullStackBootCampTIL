![[리액트 라우터.png]]

리액트 라우터를 사용하면,
사이트 내 이동을 더 편리하게 만들어 줄 수 있다.

라우터는 사이트 주소를 읽는 역할을 수행한다. 
브라우저가 가진 주소 창 값을 웹사이트에서 인지하지 못하기 때문.

### 브라우저 라우터

리액트 라우터를 사용하기 위해서는 먼저
Main 컴포넌트가 반환하는 리액트 객체를 (Main은 렌더로 바로 보내는 컴포넌트)
**BorwserRouter(브라우저 라우터)로 감싸야 한다.**

```JSX
function Main() {
  return (
    <BrowserRouter>
      <App>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/" element={<HomePage />} />
          //...
        </Routes>
      </App>
    </BrowserRouter>
  );
}
```

그리고 이를 사용하기 위해서 'react-router-dom' 임포트도 필요하다.

```JSX
import { Routes, Route, Link, useParams, Navigate } from 'react-router-dom'
//리액트 라우터 관련 함수는 'react-router-dom'에서 임포트한다.
```


- **Routes** : Route들을 묶은 것 switch문처럼 작동한다. 해당하는 주소의 Route를 찾을 때까지 순차로 읽는다.
- **Route** : 주소 경로를 가지고 해당 주소에 연결되면 보여주는 컴포넌트를 반환 (반드시 컴포넌트를 반환해야 한다.)
- **Link**: 태그를 감싸서 해당 태그를 클릭 시 'to' Prop에 정의된 주소로 이동.

1) Link는 설정한 태그(요소) 클릭시 주소를 이동해주고,
2) Routes가 사이트 주소를 읽어내고, 해당 사이트를 보여준다.

(-> Link, Routes 구현을 둘다 해줘야 사이트 이동이 가능하다.)

## 1. Routes / Route

```
<Routes>
	<Route path="/" element={<HomePage />} />
	<Route path="courses" element={<CourseListPage />} />
	<Route
		path="courses/react-frontend-development"
		element={<CoursePage />}
	/>
	<Route path="wishlist" element={<WishlistPage />} />
	<Route path="questions" element={<QuestionListPage />} />
	<Route path="questions/616825" element={<QuestionPage />} />
</Routes>
```

Routes : Route들을 감쌈. 

Route: 주소로 사이트 연결

| Prop       | 기능                                                                         |
| ---------- | -------------------------------------------------------------------------- |
| path={}    | 현재 접속한 주소가 path에 저장된 주소 값과 일치할 때까지 Route를 내려가며 읽는다.<br>(switch-case 문과 유사) |
| element={} | 일치하는 주소 값이 나오면, element에 들어 있는 컴포넌트 페이지를 표시해준다.                            |
### 1) 하위 Route

```
<Route path="courses">
	<Route index element={<CourseListPage />} />
	<Route path="react-frontend-development" element={<CoursePage />} />
</Route>
          
<Route path="questions">
	<Route index element={<QuestionListPage />} />
	<Route path="616825" element={<QuestionPage />} />
</Route>
```

하위 경로를 묶어서 정리하고 싶을 때 사용한다.
index는 상위 경로 그대로이고,
path를 설정하면 하위 경로로 연결된다.

예시는 '/courses/react-frontend-development', '/questions/616825'

### 2) 동적 경로 (useParams)

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

이렇게 설정하면 경로마다 Route를 만들어야 하는 번거로움을 해결할 수 있다.
(컴포넌트 내에서만 useParams를 받아서 잘 처리해주면 된다.)

-> 여러 경로를 **하나의 Route, 하나의 컴포넌트**로 해결

### 3) 없는 페이지 처리

```
<Routes>
	<Route path="courses">
		<Route index element={<CourseListPage />} />
		<Route path=":courseSlug" element={<CoursePage />} />
	</Route>
	...
	<Route path="*" element={<NotFoundPage />} />
</Routes>
```

( * ) **와일드 카드 문자**를 사용하면,  모든 경로에 대한 페이지를 연결할 수 있는데,
Routes의 **switch-case문의 특성**을 이용한 것이다.
마지막에 넣으면 위의 경로에 해당하지 않는 나머지 경로에 대한 처리로 해줄 수 있다.
**= 없는 페이지 처리**

### 4) 리다이렉트 (redirect)

특정 주소 경로를 다른 주소로 연결시키는 것.
없는 주소를 다른 주소로 연결해주거나 할 때 쓴다.

```JSX
import { useParams, Navigate } from 'react-router-dom'

function CorsePage(){
	const {couseSlug} = useParams();
	const course = getCouseBySlug(couresSlug);
	
	//해당하는 주소의 강의 코스가 없으면, 강의 코스 목록 페이지로 보낸다.
	if (!course){
		return <Navigate to="/courses"/>;
	}
	//... 
}
```

예시) 해당하는 주소의 강의 코스가 없으면, 강의 코스 목록 페이지로 보낸다.

## 2. Link

```
<li>
	<Link to="/courses">카탈로그</Link>
</li>
<li>
	<Link to="/questions">커뮤니티</Link>
</li>
```

| Prop  | 기능                                                                                                  |
| ----- | --------------------------------------------------------------------------------------------------- |
| to={} | 사이트 URL에 추가 경로를 지정할 수 있다.<br>'/'를 앞에 넣어서 쓰면 URL 끝에 경로가 추가된다.<br>'/'를 안써도 URL 끝에 자동으로 '/가 추가되서 연결된다. |

## 3. 커스텀 훅

리액트 라우터의 커스텀 훅. [[커스텀 훅 (RRD)]]

-  useParams - 동적 경로
-  useSearchParams - 쿼리 설정
-  useNavigate - 함수 안에 페이지 이동 기능 만들기
## 4. 싱글페이지 애플리케이션 (SPA)

라우터의 중요 특징 [[싱글 페이지 애플리케이션 (SPA)]]



---
### Routes 사용 예시 

```JSX
function Main() {
  return (
    <BrowserRouter>
      <App>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="courses" element={<CourseListPage />} />
          <Route
            path="courses/react-frontend-development"
            element={<CoursePage />}
          />
          <Route path="wishlist" element={<WishlistPage />} />
          <Route path="questions" element={<QuestionListPage />} />
          <Route path="questions/616825" element={<QuestionPage />} />
        </Routes>
      </App>
    </BrowserRouter>
  );
}
```

### Link 사용 예시

```JSX
function QuestionItem({ question }) {
  return (
    <Card className={styles.questionItem} key={question.title}>
      <div className={styles.info}>
        <Link to={'/questions/' + question.id}>
          <p className={styles.title}>
            {question.title}
            {question.answers.length > 0 && (
	              <span className={styles.count}>
		              [{question.answers.length}]
		        </span>
            )}
          </p>
        </Link>
        <p className={styles.date}>
          <DateText value={question.createdAt} />
        </p>
      </div>
      <div className={styles.writer}>
        <Avatar
          photo={question.writer.profile.photo}
          name={question.writer.name}
        />
      </div>
    </Card>
  );
}
```