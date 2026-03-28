
HTML 코드나, JS 코드가 입력 등으로 들어가서 실행되고 정보를 탈취하는 공격.

## 1️⃣ 사용자 입력값 검증 & 이스케이프 (가장 기본)

### 왜 필요?

XSS는 대부분 **사용자 입력값이 그대로 DOM에 렌더링**되면서 발생함.

### 어떻게 막아?

- 서버/클라이언트 모두에서 입력값 검증
- HTML 특수문자 이스케이프 처리


```js
// 예시 (개념)
"<script>" → "&lt;script&gt;"
```

### 효과

- 스크립트가 **문자열로만 인식**되어 실행되지 않음

---

## 2️⃣ dangerouslySetInnerHTML 사용 금지 (React 핵심)

### 왜 위험?

```jsx
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

→ 입력값에 `<script>`가 있으면 **즉시 실행**

### 실무 원칙

- **사용하지 않는다**
- 정말 필요한 경우:
    - DOMPurify 같은 sanitizer 적용

```js
DOMPurify.sanitize(userInput)
```

### 효과

- 악성 태그 / 속성 제거

---

## 3️⃣ CSP(Content Security Policy) 설정 (실무에서 강력)

### 뭐 하는 거야?

브라우저에게

> “이 사이트는 **이 출처의 스크립트만 실행해**”  
> 라고 명시하는 정책

### 예시

```http
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
```

### 효과

- 인라인 스크립트
- 외부 악성 JS 로딩  
    👉 **브라우저 단계에서 차단**

※ XSS 최종 방어선

---

## 4️⃣ HttpOnly 쿠키 사용 (토큰 탈취 방어)

### 포인트

- JS에서 `document.cookie` 접근 불가
- XSS가 발생해도 **토큰 직접 탈취 불가**

```http
Set-Cookie: accessToken=...; HttpOnly; Secure;
```

### 효과

- **XSS → 계정 탈취**로 바로 이어지는 걸 차단

---

## 5️⃣ 신뢰되지 않은 데이터 DOM 직접 삽입 금지

### 위험한 패턴

```js
element.innerHTML = userInput;
```

### 안전한 패턴

```js
element.textContent = userInput;
```

### 효과

- HTML이 아닌 **텍스트로만 처리**

---

## 6️⃣ 프레임워크 기본 보호 기능 활용

### React / Vue

- JSX는 기본적으로 **자동 이스케이프**
- `{userInput}`은 안전

### 주의

- 자동 보호를 **무력화하는 순간(XSS 지뢰)**만 조심

---

## 면접용 요약 답변 💬 (이거 외워도 됨)

> “XSS는 사용자 입력값이 스크립트로 실행되는 게 원인이라  
> 입력값 이스케이프, dangerouslySetInnerHTML 지양,  
> CSP 설정, HttpOnly 쿠키 사용으로 다층 방어를 합니다.”

---

## 실무 우선순위 정리

1️⃣ 입력값 검증 / 이스케이프  
2️⃣ 위험한 DOM API 사용 금지  
3️⃣ CSP 적용  
4️⃣ 토큰은 HttpOnly 쿠키로 보호

이 흐름을 말할 수 있으면  
👉 **‘보안 개념 아는 신입’이 아니라 ‘설계한 신입’**으로 보여.

원하면 다음도 이어서 정리해줄게:

- 🔐 CSRF 방어는 어떻게 설계하나요?
- 🔐 XSS가 뚫려도 피해를 최소화하는 구조
- 🔐 실제 서비스 보안 레벨별 토큰 저장 전략

다음 뭐 갈까?