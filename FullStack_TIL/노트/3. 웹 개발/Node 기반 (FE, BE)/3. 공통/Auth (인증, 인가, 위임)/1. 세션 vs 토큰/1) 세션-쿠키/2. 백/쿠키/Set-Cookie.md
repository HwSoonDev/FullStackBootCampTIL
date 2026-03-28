백엔드에서 프론트 브라우저 쿠키에 자동으로 값을 저장하는 방법.

## 쿠키 옵션 설정

```js
res.cookie('authToken', token, {
  httpOnly: true, // 요거는 조금 이따가!
  secure: process.env.NODE_ENV === 'production', // 개발 환경에서는 false, 프로덕션에서는 true
  // 기타 속성...
});
```

```js
res.cookie( 값이름, 값, {
	httpOnly: true, //활성화여부
	secure: true, //활성화 여부
	sameSite: 'None'
})
```

- secure: https만 사용하는 보안설정.
- httpOnly: 쿠키 외부 열람 불가능 설정 ( js 접근, 개발자 도구 열람 x)
- sameSite: 같은 도메인의 사이트에서만 사용 가능
	- `'None'` 다른 사이트도 허용
	- `'Lax'` '느슨한'
	- `'Strict'` 같은 사이트만 허용

## 배포 시 설정
```js
res.cookie( 값이름, 값, {
	httpOnly: true, //js 접근 불가
	secure: true, //https(보안)에서만 작동
	sameSite: 'None' // 도메인이 달라도 사용 가능.
})
```

## 개발 시 설정
 보통 로컬 (http://localhost) 에서 실험하고, 사이트도 같지 않을 경우가 많아서
 아래 방식이 보편적인 설정.
```js
res.cookie( 값이름, 값, {
	httpOnly: true, //js 접근은 필요 없으므로 true
	secure: false, //로컬은 http 기반으로 https가 아니라도 되도록 false
	sameSite: 'Lax' //secure: false 상태에서는 sameSite: 'None'은 작동 x
})
```