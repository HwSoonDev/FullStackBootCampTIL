서버를 배포하고, 포트를 외부에 노출한 다음에는 어떻게 도메인 주소를 연결할까?
공인 IP를 내가 만든 서버 URL에 연결하는 방법을 알아보았습니다.

## 중요 개념

도메인 =  “이름”,
DNS =  “주소 연결 (실제 숫자 주소와 이름 연결)”, 
HTTPS = “보안 인증서”

https 도메인을 연결하는 과정은 “도메인 구매 → 서버 연결 → HTTPS 적용” 이렇게 세 단계로 이해하면 깔끔합니다. 
## 1️⃣ 도메인 구매

도메인은 말 그대로 “서버 주소 대신 사용할 이름”입니다.

대표적인 서비스:

* 가비아
* Namecheap
* GoDaddy

예를 들어 `myapp.com` 같은 걸 구매합니다.

---

## 2️⃣ 도메인 → 서버 연결 (DNS 설정)

도메인을 샀다고 바로 연결되는 게 아니라, **“이 도메인이 어느 서버를 가리킬지” 설정**해야 합니다.

여기서 중요한 개념:

* **A 레코드**: 도메인 → IP 연결

예시:

```
myapp.com → 123.123.123.123 (서버 IP)
```

1) **공유기 포트포워딩**
   서버에서 열어둔 내부 IP(+포트)가 외부 IP(+포트) 와 연결될 수 있도록 설정을 진행합니다.
   ![[Pasted image 20260329181231.png]]
2) **도메인의 호스팅 외부 IP를 등록합니다 ( A 레코드 )**
   저는 카페24를 이용해서 도메인을 구매했습니다.
   ![[Pasted image 20260329181556.png]]
3) 

---

## 3️⃣ Nginx로 도메인 연결

이제 서버에 요청이 오면 Nginx가 받아야 합니다.

예시 설정:

```nginx
server {
    listen 80;
    server_name myapp.com;

    location / {
        proxy_pass http://localhost:3000;
    }
}
```

의미:

* `myapp.com`으로 들어온 요청 → 3000번 서버로 전달

👉 도커라면:

```
proxy_pass http://web:3000;
```

(컨테이너 이름 사용)

---

## 4️⃣ HTTPS 적용 (핵심)

여기서 HTTPS가 등장합니다.

무료 인증서 발급 서비스:

* Let's Encrypt

보통은 이걸 사용합니다.

---

### 방법: Certbot 사용

대표 툴:

* Certbot

설치 후 실행:

```bash
sudo certbot --nginx -d myapp.com
```

그러면 자동으로:

1. 인증서 발급
2. Nginx 설정 수정
3. HTTPS 적용

---

### 적용 후 Nginx 모습

```nginx
server {
    listen 443 ssl;
    server_name myapp.com;

    ssl_certificate /etc/letsencrypt/live/myapp.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/myapp.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
    }
}
```

---

## 5️⃣ HTTP → HTTPS 강제 리다이렉트

보통 이렇게 추가합니다:

```nginx
server {
    listen 80;
    server_name myapp.com;
    return 301 https://$host$request_uri;
}
```

---

## 전체 흐름 정리

조금 추상적으로 보면 이렇게 이어집니다:

```
[사용자]
   ↓
https://myapp.com
   ↓
DNS (도메인 → IP)
   ↓
서버 (Nginx)
   ↓
Docker 컨테이너 (Next.js / API)
```

---

## 🔥 당신 상황 기준 핵심 포인트

지금까지 질문 흐름을 보면:

* 도커 사용 중
* Nginx 사용 예정
* 라즈베리 or 개인 서버

👉 그래서 가장 중요한 건 이 3가지입니다:

1. **공인 IP 확보 (또는 DDNS)**
2. **공유기 포트포워딩 (80, 443)**
3. **Nginx + Certbot 연결**
