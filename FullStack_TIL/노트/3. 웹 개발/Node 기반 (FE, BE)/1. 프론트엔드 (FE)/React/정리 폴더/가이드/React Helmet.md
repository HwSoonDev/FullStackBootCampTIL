페이지 제목 바꾸기
우리가 만든 프로젝트에서 한 가지 아쉬운 점이 있다면
각 페이지에 들어갔을 때 페이지 제목이 바뀌지 않는다는 건데요.
아래 스크린샷들에서 주소창 위에 제목이 보이시나요?


![uzugu5wf1-home.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=5120&directory=uzugu5wf1-home.png&name=uzugu5wf1-home.png)

![4s8lo0rb6-catalog.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=5120&directory=4s8lo0rb6-catalog.png&name=4s8lo0rb6-catalog.png)

페이지를 이동하더라도 제목이 “Codethat”으로 그대로 유지되고 있습니다.
이 제목은 public 폴더의 index.html 파일에 있는 `<title>` 태그에 지정된 건데요.
개발자 도구를 열어서 보면 보이는 HTML 코드에서 `head` 태그 안에 있는 `title` 태그가 바로 그것이죠.

```html
<html lang="ko">
  <head>
    <meta charset="utf-8" />
    <title>Codethat</title>
    ...
  </head>
  <body>
    <div id="root" class="container">
      ...
    </div>
    <script src="/static/js/bundle.js"></script>
    <script src="/static/js/vendors~main.chunk.js"></script>
    <script src="/static/js/main.chunk.js"></script>
  </body>
</html>
```

자바스크립트로 페이지 제목을 바꾸려면 아래처럼 `document.title` 값을 수정하면 됩니다.

```JSX
document.title = 'Codethat - 커뮤니티';
```

그런데 이 코드의 문제점은 페이지 제목이 어떻게 바뀌는지 알려면,
관련된 코드를 차례대로 읽어봐야 한다는 단점이 있죠. 눈에 잘 띄지도 않고요.
이것보다 좀 더 편리하게 리액트스러운 방법으로 제목을 바꿀 수 있는 방법이 있습니다.
바로 `react-helmet` 이라는 라이브러리를 사용하는 건데요.

```JSX
import { Helmet } from 'react-helmet';
import Button from '../components/Button';
import Container from '../components/Container';
import Lined from '../components/Lined';
import styles from './HomePage.module.css';
import landingImg from '../assets/landing.svg';

function HomePage() {
  return (
    <>
      <Helmet>
        <title>Codethat - 코딩이 처음이라면, 코드댓</title>
      </Helmet>
      <div className={styles.bg} />
      <Container className={styles.container}>
        <div className={styles.texts}>
          <h1 className={styles.heading}>
            <Lined>코딩이 처음이라면,</Lined>
            <br />
            <strong>코드댓</strong>
          </h1>
          <p className={styles.description}>
            11만 명이 넘는 비전공자, 코딩 입문자가 코드댓 무제한 멤버십을
            선택했어요.
            <br />
            지금 함께 시작해보실래요?
          </p>
          <div>
            <Button>지금 시작하기</Button>
          </div>
        </div>
        <div className={styles.figure}>
          <img src={landingImg} alt="그래프, 모니터, 윈도우, 자물쇠, 키보드" />
        </div>
      </Container>
    </>
  );
}

export default HomePage;

```
사용법은 정말 간단합니다.

예를 들면 위 코드처럼 `Helmet` 이라는 컴포넌트로 감싼 다음에,
안에다가 `<title>` 태그를 배치하면 이 컴포넌트가 렌더링 될 때
HTML의 `<title>` 태그를 덮어쓸 수 있습니다.
이름이 헬멧인 이유는 `<head>` 태그에 덮어쓰기 때문이죠 😆.
관심있으신 분들은 아래 사이트를 방문해서 사용법을 참고해서 적용해보면 좋을 것 같습니다!