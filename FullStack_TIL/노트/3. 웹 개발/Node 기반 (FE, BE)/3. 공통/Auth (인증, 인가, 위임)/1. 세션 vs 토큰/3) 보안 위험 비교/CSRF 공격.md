## CSRF란?

**CSRF (Cross-Site Request Forgery)**  
👉 _사용자가 원하지 않았는데도, 사용자인 척 요청이 날아가게 만드는 공격_



> 🔥 **쿠키는 “자동으로” 전송된다**
## 공격 시나리오

### 1️⃣ 정상 상태

- 너는 이미 로그인돼 있음
- 브라우저에 로그인 쿠키 있음

`example.com  ← 로그인 쿠키 있음`

---

### 2️⃣ 악성 사이트 방문

너가 이런 사이트를 실수로 들어감:
```html
<img src="https://example.com/api/transfer?money=1000000" />
```

---

### 3️⃣ 브라우저의 행동 (문제 포인트)

브라우저는 이렇게 생각함:

> “어? example.com 요청이네?  
> 쿠키 있지? **자동으로 같이 보낸다**.”

```http
GET /api/transfer?money=1000000 
Cookie: session=abcd1234
```

---

### 4️⃣ 서버의 착각

서버는:

> “오 로그인한 사용자네?  
> 그럼 이체 실행 😱”

👉 **너는 아무것도 안 했는데 돈이 빠짐**

이게 CSRF야.

---

## 왜 쿠키 방식에서만 문제가 되냐?

|저장 위치|자동 전송|CSRF 위험|
|---|---|---|
|Cookie|✅ 자동|❌ 위험|
|localStorage|❌ 수동|거의 없음|

👉 로컬 토큰은 **JS가 직접 넣어야 해서**  
다른 사이트가 대신 요청 못 날려.

---

## 그래서 “쿠키는 위험하다”는 말이 나온 거야

정확히는:

> ❌ 쿠키 자체가 위험  
> ✅ **CSRF 대비 없이 쿠키 쓰는 게 위험**

---

## CSRF 방어 방법 (실무 필수)

### 1️⃣ **SameSite 쿠키**

`Set-Cookie: session=xxx; SameSite=Lax`

- 다른 사이트 → 자동 전송 안 됨
- 대부분의 공격 1차 차단

---

### 2️⃣ **CSRF 토큰 (가장 확실)**

`서버가 토큰 발급 → 클라이언트가 요청 헤더에 포함 → 서버가 비교`

`POST /api/action Cookie: session=xxx X-CSRF-Token: abc123`

👉 **공격자는 이 토큰을 모른다**

---

### 3️⃣ **POST / PUT만 허용**

- 상태 변경 API를 GET으로 만들면 ❌
- 브라우저 자동 요청 차단

---

## Next.js + 쿠키 + JWT 실무 패턴 요약

`Access Token  → HttpOnly Cookie (짧게) Refresh Token → HttpOnly Cookie CSRF Token   → 헤더`

이렇게 하면:

- XSS ❌
- CSRF ❌
- 미들웨어 인증 가능 ✅

---

## 한 문장 요약

> 🛡️ **CSRF는 “쿠키가 자동 전송되는 성질”을 악용한 공격이고  
> 해결책은 ‘쿠키 + 추가 검증(토큰)’이다**