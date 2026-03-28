## JWT 로컬 인증 구조

- 로그인 성공 시 accessToken을 로컬스토리지에 저장하고, 사용자 정보는 메모리 상태로 관리한다.
- 앱 시작 시 저장된 토큰 유무를 확인하고 사용자 정보를 복구하거나 갱신을 시도한다.
- 로컬스토리지 기반은 서버/미들웨어에서 접근이 불가해 이동 전 차단에 한계가 있다.

예시 코드:

```ts
// 로그인 성공 후 토큰 저장
localStorage.setItem('accessToken', accessToken);

// 앱 시작 시 토큰 확인
const token = localStorage.getItem('accessToken');
if (token) {
  // /auth/me 요청으로 사용자 복구
} else {
  // /auth/refresh 시도
}
```

  

## API 요청과 토큰 갱신

- 요청 시 `Authorization: Bearer {accessToken}` 헤더를 주입한다.
- 401 응답이면 토큰을 갱신하고 원 요청을 재시도한다.
- 동시 갱신은 단일 갱신 + 대기열 방식으로 중복 요청을 방지한다.

예시 코드:
(ky 라이브러리 방식)
```ts
// 요청 전 토큰 주입
if (token) {
  request.headers.set('Authorization', `Bearer ${token}`);
}

// 401 대응 흐름
if (response.status === 401) {
  const newToken = await refreshAccessToken();
  request.headers.set('Authorization', `Bearer ${newToken}`);
  return retryRequest(request);
}
```

  
## 라우트 가드(클라이언트 측)

- `AuthGuard`는 인증이 없으면 로그인 페이지로 리다이렉트한다.
- `LoginedGuard`는 이미 로그인된 사용자의 로그인/회원가입 접근을 막는다.
- 가드는 렌더 후 `useEffect`에서 검사하므로 이동 전 차단이 아니다.
  
예시 코드:
```tsx
useEffect(() => {
  if (isUserLoaded && !user) {
    router.replace(`/login?returnUrl=${encodeURIComponent(pathname)}`);
  }
}, [isUserLoaded, user, pathname, router]);
  
if (!isUserLoaded) return null;
return <>{children}</>;
```

  
## 리다이렉트 동작

- `router.replace`는 히스토리를 대체해 뒤로가기로 보호 페이지에 복귀하지 못하게 한다.
- `router.push`는 히스토리를 추가해 뒤로가기가 가능하다.
- `returnUrl`은 쿼리 파라미터로 전달되며, 인코딩이 필요하다.


예시 코드:

```ts

// 뒤로가기로 다시 접근하지 못하게 함

router.replace(`/login?returnUrl=${encodeURIComponent(pathname)}`);

  

// 일반 이동 (히스토리 추가)

router.push('/');

```

  

## 인코딩 관련

- `encodeURIComponent`는 쿼리 파라미터에 안전하게 넣기 위해 특수문자를 퍼센트 인코딩한다.

- 자바스크립트 표준 내장 함수로 별도 import 없이 사용할 수 있다.

  

예시 코드:

```ts

encodeURIComponent('/user/estimates?tab=1');

// => "%2Fuser%2Festimates%3Ftab%3D1"

```

  

## 미들웨어 가드 적용 여부와 최적화

- 로컬스토리지 토큰 구조에서는 미들웨어 가드를 쓰기 어렵다.

- 이동 전 차단을 원하면 httpOnly 쿠키 기반 구조로 전환이 필요하다.

- UX/보안 측면에서 유리하지만 구조 변경 비용이 발생한다.

  

예시 코드:

```ts

// 미들웨어에서 쿠키 기반 판단 (개념 예시)

const token = cookies().get('accessToken');

if (!token) return NextResponse.redirect(new URL('/login', req.url));

```

  

## 로딩 시 화면 상태

- 가드가 로딩 중일 때 본문이 렌더되지 않아 GNB만 보일 수 있다.

  

예시 코드:

```tsx

if (!isUserLoaded) {

  return <LoadingSpinner />;

}

```