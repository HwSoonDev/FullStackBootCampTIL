
Nginx는 내부 포트를 외부에 노출하지 않고 
특정 포트만 열기 위해 사용하는 프로그램입니다.

![[Pasted image 20260329142652.png]]

보통 도커 사용시, nginx는 프로젝트 폴더에 폴더를 생성하여 
안에 config 파일만 작성합니다. 이미지를 컨테이너에 넣을 때, nginx 설정을 할 떄 사용하는 설정 파일입니다.

![[Pasted image 20260329143238.png]]
Nginx가 `'/' 경로는 'web'이라는 컨테이너의 3000번 포트로 연결한다` 라고 설정한 것입니다.

예시)

| 경로   | 컨테이너 | 포트   |
| ---- | ---- | ---- |
| /    | web  | 3000 |
| /api | api  | 4000 |

## 예시 코드

//default.config
```default.config
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

그리고 yml에서 열어두었던 프론트와 백의 포트를 닫고,
nginx를 추가합니다.

프론트, 백을 위한 환경 변수의 URL들도,
nignx에서 설정한 루트 URL에 프론트, 백을 위해 설정한 URl 경로를 붙어 재설정합니다.

```docker-compose.yml
services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: atashinchi_db
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d atashinchi_db"]
      interval: 5s
      timeout: 5s
      retries: 10

  api:
    build:
      context: ./back-end
      dockerfile: Dockerfile
    ports:
      - "4000:4000" <-이거는 제거
    environment:
      PORT: "4000"
      DATABASE_URL: postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/atashinchi_db?schema=public
      SESSION_SECRET: ${SESSION_SECRET}
      CORS_ORIGIN: ${CORS_ORIGIN}
      NODE_ENV: production
      TRUST_PROXY: "true"
    depends_on:
      db:
        condition: service_healthy

  web:
    build:
      context: ./front-end
      dockerfile: Dockerfile
      args:
        NEXT_PUBLIC_API_URL: ${NEXT_PUBLIC_API_URL}
    ports:
      - "3000:3000" <-이거는 제거
    environment:
      NODE_ENV: production
    depends_on:
      - api

  # Entry: http://localhost — use NEXT_PUBLIC_API_URL=http://localhost/api and CORS_ORIGIN=http://localhost
  nginx:
    image: nginx:1.27-alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - web
      - api

volumes:
  pgdata:
```

