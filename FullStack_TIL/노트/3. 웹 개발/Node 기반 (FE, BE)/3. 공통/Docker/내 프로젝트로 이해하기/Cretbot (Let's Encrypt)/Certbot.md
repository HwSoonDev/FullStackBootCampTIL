https 인증서 발급 프로그램 (CLI) (무료!)


**Certbot은 “HTTPS 인증서를 자동으로 발급하고 관리해주는 도구”입니다.**

조금 더 풀어서 말하면:

- HTTPS를 쓰려면 **SSL 인증서**가 필요하고
- 그 인증서를 무료로 발급해주는 곳이 👉 Let's Encrypt
- 그리고 그 발급 과정을 자동으로 해주는 프로그램이 👉 Certbot 입니다


```
1. Let's Encrypt에 요청
2. "이 도메인 네 거 맞아?" 검사
3. nginx 통해 확인 (http://도메인/.well-known/...)
4. 통과하면 인증서 발급
5. 서버에 저장
```

Nginx vs Certbot
```
Certbot → 인증서 발급 담당
Nginx → HTTPS 처리 담당
```

# Let's Encrypt (Https 인증서 발급 기관)

![[Pasted image 20260329175058.png]]

# Certbot (인증서 자동 관리 프로그램)

![[Pasted image 20260329175205.png]]
