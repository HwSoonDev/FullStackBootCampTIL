백엔드에서 프론트 브라우저 쿠키에 자동으로 값을 저장하는 방법.

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
	secure: true //활성화 여부
})
```

- secure: https만 사용하는 보안설정.
- httpOnly: 쿠키 외부 열람 불가능 설정 ( js 접근, 개발자 도구 열람 x)
- 