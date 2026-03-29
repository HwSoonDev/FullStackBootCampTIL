
```docker-compose.yml
 nginx:
    image: nginx:1.27-alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - certbot_www:/var/www/certbot:ro
      - certbot_etc:/etc/letsencrypt:ro
    depends_on:
      - web
      - api

  # 최초 발급(한 번): docker compose run --rm certbot certonly --webroot -w /var/www/certbot -d example.com --email you@example.com --agree-tos --no-eff-email
  # (example.com·이메일을 실제 도메인·메일로 바꿀 것. 도메인 DNS가 이 서버 80으로 향해야 함.)
  certbot:
    image: certbot/certbot:v2.11.0
    restart: unless-stopped
    volumes:
      - certbot_etc:/etc/letsencrypt
      - certbot_var:/var/lib/letsencrypt
      - certbot_www:/var/www/certbot
    entrypoint: /bin/sh -c "trap exit TERM; while :; do certbot renew; sleep 12h & wait $$!; done;"

volumes:
  pgdata:
  certbot_etc:
  certbot_var:
  certbot_www:
```

이 설정은 **Nginx + Certbot을 함께 사용해서 HTTPS(SSL 인증서 자동 발급/갱신)**까지 처리하는 구조입니다. 이미 꽤 제대로 된 “실서비스용 배포 구조”에 가까워요. 하나씩 차근히 풀어보겠습니다.

---

## 1) Nginx 서비스

```yaml
nginx:
  image: nginx:1.27-alpine
```

→ Nginx 공식 이미지 사용
→ `alpine`은 가벼운 리눅스라서 서버용으로 좋습니다

---

```yaml
ports:
  - "80:80"
```

→ 외부(인터넷)의 **80번 포트 요청을 이 컨테이너로 연결**

즉,

* 사용자가 `http://도메인` 접속 → Nginx가 받음

---

```yaml
volumes:
  - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
```

→ 네가 만든 Nginx 설정 파일을 컨테이너에 넣는 것

* 왼쪽: 내 PC 파일
* 오른쪽: 컨테이너 내부 경로
* `:ro` = 읽기 전용 (안전하게)

---

```yaml
  - certbot_www:/var/www/certbot:ro
```

→ 인증서 발급용 **웹 루트 공유**

이게 중요한 포인트인데:

* Certbot이 인증 파일을 여기 저장
* Nginx가 그 파일을 외부에 노출

👉 즉, 둘이 협업하는 공간

---

```yaml
  - certbot_etc:/etc/letsencrypt:ro
```

→ 실제 발급된 SSL 인증서 저장 위치

* Certbot이 여기에 인증서 생성
* Nginx가 여기서 읽어서 HTTPS에 사용

---

```yaml
depends_on:
  - web
  - api
```

→ Nginx는 web, api가 먼저 실행된 후 시작

즉 구조는:

```
사용자 → nginx → web / api
```

---

## 2) Certbot 서비스 (핵심)

```yaml
certbot:
  image: certbot/certbot:v2.11.0
```

→ Let's Encrypt 인증서를 발급해주는 도구

---

```yaml
restart: unless-stopped
```

→ 컨테이너가 죽으면 자동 재시작

---

```yaml
volumes:
  - certbot_etc:/etc/letsencrypt
  - certbot_var:/var/lib/letsencrypt
  - certbot_www:/var/www/certbot
```

각각 의미:

* `/etc/letsencrypt` → 인증서 저장
* `/var/lib/letsencrypt` → 내부 데이터
* `/var/www/certbot` → 인증 파일 (Nginx랑 공유)

---

```yaml
entrypoint: /bin/sh -c "trap exit TERM; while :; do certbot renew; sleep 12h & wait $$!; done;"
```

이건 꽤 중요한 로직입니다.

### 의미:

* 무한 루프 실행
* 12시간마다 `certbot renew` 실행

즉:

👉 **인증서 자동 갱신 시스템**

(SSL 인증서는 90일짜리라서 필수)

---

## 3) 인증서 최초 발급 명령

```bash
docker compose run --rm certbot certonly \
  --webroot -w /var/www/certbot \
  -d example.com \
  --email you@example.com \
  --agree-tos --no-eff-email
```

이건 "딱 한 번만" 실행

### 의미:

* `--webroot` → nginx를 통해 인증
* `-w /var/www/certbot` → 인증 파일 위치
* `-d example.com` → 도메인
* 이메일 → 인증서 만료 알림용

---

### 이 과정이 어떻게 동작하냐면:

1. certbot이 인증 파일 생성
2. nginx가 그 파일을 외부에 공개
3. Let's Encrypt 서버가 확인
4. 인증서 발급 완료

---

## 4) volumes (맨 아래)

```yaml
volumes:
  pgdata:
  certbot_etc:
  certbot_var:
  certbot_www:
```

→ 도커 볼륨 선언

각 역할:

* `pgdata` → DB 저장
* `certbot_etc` → 인증서
* `certbot_var` → certbot 내부 데이터
* `certbot_www` → nginx ↔ certbot 공유 공간

👉 핵심: 컨테이너가 삭제돼도 데이터 유지

---

## 전체 구조 정리

이 구성은 이렇게 흐릅니다:

```
[사용자]
   ↓
http://example.com
   ↓
[Nginx]
   ↓
 ┌───────┬───────┐
 │  web  │  api  │
 └───────┴───────┘

+ 인증서 시스템
Certbot ↔ Nginx (파일 공유)
```

---

## 한 줄 요약

이 설정은:

👉 **Nginx가 트래픽을 받고**
👉 **Certbot이 SSL 인증서를 자동 발급 & 갱신하고**
👉 **둘이 볼륨으로 연결된 완전한 HTTPS 서버 구조**

---

## 중요한 체크 포인트 (실전에서 자주 막힘)

* 도메인이 서버 IP로 연결돼 있어야 함 (DNS)
* 80 포트 열려 있어야 함
* nginx에서 `/var/www/certbot` 경로를 처리하도록 설정 필요