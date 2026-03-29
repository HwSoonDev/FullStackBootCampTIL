쿠키 설정 옵션이는 중요하게 3가지가 있습니다.

1) HttpOnly
2) Secure
3) SameSite

개발 시 편의를 위해 풀어둘 떄의 설정과,
배포 시 강력하게 제한하는 설정이 다르기 때문에 외워둡니다.

| 옵션       | 개발 시  | 배포 시 | 설명                                                                                                                                                             |
| -------- | ----- | ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HttpOnly | true  | true | JS에서 `document.cookie`로 못 읽게 해 XSS로 세션 탈취하기 어렵게 하는 용도로, 개발 시에도 true로 둡니다.                                                                                      |
| Secure   | false | true | https만 허용할 것인지 여부입니다.<br>개발 시에는 http인 경우가 많으므로 임시로 false로 둡니다.                                                                                                 |
| SameSite | lax   | lax  | 크로스 사이트 요청에 쿠키를 허용할 것인지 여부입니다.<br>우리 도메인에서 보내는 api 정도만 크로스 사이트를 허용하는 `lax`가 일반 적으로 쓰입니다.  (개발 시에도)<br>완전히 같은 사이트로만 제한하고 싶으면 `strict`,<br>완전히 풀고 싶으면 `none`입니다. |

```json
cookie: {
    httpOnly: true,
    secure: secureCookie,
    sameSite,
    maxAge: Number(process.env.SESSION_MAX_AGE_MS) || 1000 * 60 * 60 * 24 * 7,
    path: "/",
    ...(cookieDomain ? { domain: cookieDomain } : {}),
  },
```

실제 **공개 서비스(프로덕션)** 에서는 보통 아래처럼 맞춥니다. 지금 프로젝트의 `session.ts` 설정과도 그대로 연결됩니다.

## 필수에 가까운 것

- **`Secure: true`**  
  사용자 브라우저까지 **HTTPS**로만 서비스할 때 켭니다. HTTP로는 세션 쿠키를 보내지 않게 해서 중간자·유출 위험을 줄입니다.  
  배포가 **TLS 종료(nginx, 로드밸런서, CDN)** 까지 HTTPS면 `SESSION_COOKIE_SECURE=true` (또는 env 없이 production 기본이 true인 쪽 로직)가 맞습니다.

- **`HttpOnly: true`**  
  이미 코드에 있습니다. JS에서 `document.cookie`로 못 읽게 해 XSS로 세션 탈취하기 어렵게 합니다.

- **`SESSION_SECRET`**  
  길고 예측 불가능한 값, 프로덕션에서 반드시 설정 (지금도 production에서 없으면 throw).

- **리버스 프록시 뒤**  
  `trust proxy` + 실제 스킴이 HTTPS인지 알 수 있게 프록시에서 `X-Forwarded-Proto` 등을 넘기는 구성. 지금은 `TRUST_PROXY`로 켜 두었습니다.

## 상황에 따라

- **`SameSite`**  
  - **`Lax`**(기본): 같은 사이트 + 일부 크로스 사이트 GET 네비게이션에서 쿠키 전송. 대부분의 “우리 도메인 웹앱 + API 같은 도메인/경로”에 무난합니다.  
  - **`Strict`**: 조금 더 빡세게, 크로스 사이트에서의 요청에는 쿠키 안 붙음.  
  - **`None`**: **다른 도메인** 프론트가 이 API에 `credentials`로 붙여야 할 때만. 이때는 **`Secure` 필수**입니다.

- **`Domain` (`SESSION_COOKIE_DOMAIN`)**  
  `www.example.com`과 `app.example.com`처럼 **서브도메인 간에 같은 세션**을 쓸 때만 `.example.com` 형태로 지정합니다. 한 도메인만 쓰면 **비우는 편**이 안전합니다(실수로 범위 넓히기 방지).

- **`maxAge` / 만료**  
  서비스 정책에 맞게. 지금은 env로 조절 가능합니다.

## 정리 표

| 항목 | 실제 배포(HTTPS)에서 흔한 값 |
|------|------------------------------|
| `secure` | `true` |
| `httpOnly` | `true` |
| `sameSite` | `lax` 또는 요구사항에 따라 `strict` / `none` |
| `domain` | 보통 미설정; 서브도메인 공유 시에만 |
| `path` | `/` (지금과 동일) |

LAN/내부용으로 **HTTP만** 쓰는 배포는 예외적으로 `Secure`를 끄지만, **인터넷에 노출되는 프로덕션**은 HTTPS + `Secure: true`를 전제로 두는 것이 표준입니다.