
Certbot을 위한 Nginx 설정 부분입니다.

```
    # Certbot HTTP-01 (docker compose certbot 서비스와 certbot_www 볼륨 공유)
    location ^~ /.well-known/acme-challenge/ {
        root /var/www/certbot;
        default_type "text/plain";
    }
```

이건 Let’s Encrypt가 `“이 도메인을 네가 실제로 쓰고 있니?”`를 확인할 때 쓰는 주소로 들어오는 요청을, ==Certbot이 만든 작은 파일로 응답==하게 하는 설정입니다.

### `location ^~ /.well-known/acme-challenge/ { ... }`

- 브라우저용 주소가 아니라, **인증 기관(CA)이 검증용으로만 호출**하는 경로입니다.
- `^~` 는 “이 prefix와 맞으면 다른 `location` 규칙보다 **우선**해라”는 뜻이라, `/` 로 가는 프록시보다 먼저 여기서 처리됩니다.
- 그래서 `http://도메인/.well-known/acme-challenge/토큰파일이름` 요청이 왔을 때 **Next/API로 넘기지 않고** nginx가 직접 파일을 줄 수 있습니다.

### `root /var/www/certbot;`

- 요청 경로와 디스크 경로를 이렇게 붙입니다:  
  `URI` → `root` + `URI`
- 예: `/.well-known/acme-challenge/abc123` → 파일 **`/var/www/certbot/.well-known/acme-challenge/abc123`**
- Certbot(docker)은 **같은 볼륨** `certbot_www` 안에 그 경로에 파일을 써 두고, nginx는 그걸 읽어서 CA에 보여 줍니다.  
  CA가 그 내용을 확인하면 **인증서 발급/갱신**이 진행됩니다.

### `default_type "text/plain";`

- 응답의 `Content-Type`을 평문으로 맞춥니다.  
  ACME 검증은 보통 짧은 텍스트 한 줄이라, 타입을 명시해 두는 정도의 안전장치에 가깝습니다.

**한 줄 요약:**  
“`/.well-known/acme-challenge/` 로 오는 건 **HTTPS 인증서 발급용 검증 파일**이니, Docker Certbot이 넣은 `/var/www/certbot` 아래 파일로 그대로 내려줘라”는 뜻입니다.


### 전체 코드 (참고용)

```
# Compose 네트워크에서 upstream: web(Next.js :3000), api(Express :4000)
# 컨테이너에 마운트 예: /etc/nginx/conf.d/default.conf

map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

server {
    listen 80;
    listen [::]:80;
    server_name _;

    client_max_body_size 20m;

    # Certbot HTTP-01 (docker compose certbot 서비스와 certbot_www 볼륨 공유)
    location ^~ /.well-known/acme-challenge/ {
        root /var/www/certbot;
        default_type "text/plain";
    }

    location /api {
        proxy_pass http://api:4000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        proxy_pass http://web:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
```