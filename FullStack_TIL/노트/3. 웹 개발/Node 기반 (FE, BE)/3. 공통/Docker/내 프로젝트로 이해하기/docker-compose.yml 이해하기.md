```yml
# 로컬·단일 머신용: docker compose up --build
# LAN에서 접속 시: web 이미지 빌드 전 NEXT_PUBLIC_API_URL·CORS_ORIGIN을 호스트 IP에 맞춤
#   NEXT_PUBLIC_API_URL=http://192.168.x.x:3001 CORS_ORIGIN=http://192.168.x.x:3000 docker compose up --build

services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
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
      - "3001:3001"
    environment:
      PORT: "3001"
      DATABASE_URL: postgresql://postgres:postgres@db:5432/atashinchi_db?schema=public
      SESSION_SECRET: ${SESSION_SECRET:-change-me-in-production}
      CORS_ORIGIN: ${CORS_ORIGIN:-http://localhost:3000}
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
        NEXT_PUBLIC_API_URL: ${NEXT_PUBLIC_API_URL:-http://localhost:3001}
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
    depends_on:
      - api

volumes:
  pgdata:

```

# 🔷 전체 구조 한눈에

```
[ web (3000) ]  →  [ api (3001) ]  →  [ db (5432) ]
   프론트           백엔드            PostgreSQL
```

---

# 1️⃣ 맨 위 주석 (사용 방법)

```yml
# docker compose up --build
```

👉 이미지를 빌드하고 실행

```yml
# NEXT_PUBLIC_API_URL=http://192.168.x.x:3001 ...
```

👉 같은 Wi-Fi(LAN)에서 다른 기기로 접속하려면
**localhost 대신 내 PC IP를 써야 한다는 의미**

---

# 2️⃣ services: (핵심)

여기서 컨테이너 3개를 정의함

---

## 🟣 1. db (PostgreSQL)

```yml
db:
  image: postgres:16-alpine
```

👉 PostgreSQL 16 경량 버전 사용

### 환경 변수

```yml
POSTGRES_USER: postgres
POSTGRES_PASSWORD: postgres
POSTGRES_DB: atashinchi_db
```

👉 DB 계정/비밀번호/DB 이름 설정

---

### 데이터 영구 저장

```yml
volumes:
  - pgdata:/var/lib/postgresql/data
```

👉 컨테이너 꺼져도 DB 데이터 유지됨

---

### 포트

```yml
5432:5432
```

👉 내 컴퓨터에서도 DB 접속 가능

---

### 헬스 체크 (중요)

```yml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres -d atashinchi_db"]
```

👉 DB가 **정상적으로 준비됐는지 확인**

---

## 🟢 2. api (백엔드)

```yml
build:
  context: ./back-end
```

👉 `./back-end/Dockerfile`로 이미지 생성

---

### 포트

```yml
3001:3001
```

👉 백엔드 서버

---

### 환경 변수 핵심

```yml
DATABASE_URL: postgresql://postgres:postgres@db:5432/atashinchi_db
```

👉 중요 포인트:

* `db` ← 이건 서비스 이름 (컨테이너 DNS)
* 즉 👉 **DB 컨테이너로 직접 연결**

---

```yml
SESSION_SECRET: ${SESSION_SECRET:-change-me-in-production}
```

👉 환경변수 없으면 기본값 사용

---

```yml
CORS_ORIGIN: ${CORS_ORIGIN:-http://localhost:3000}
```

👉 프론트에서 요청 허용할 주소

---

### 실행 순서 제어 (핵심)

```yml
depends_on:
  db:
    condition: service_healthy
```

👉 DB 준비 완료 후 api 실행

---

## 🔵 3. web (프론트)

```yml
build:
  context: ./front-end
```

👉 프론트 프로젝트 빌드

---

### build args (중요)

```yml
NEXT_PUBLIC_API_URL: ${NEXT_PUBLIC_API_URL:-http://localhost:3001}
```

👉 프론트 코드에 들어가는 API 주소

(Next.js라면 `process.env.NEXT_PUBLIC_API_URL`)

---

### 포트

```yml
3000:3000
```

---

### 실행 순서

```yml
depends_on:
  - api
```

👉 api 먼저 실행 후 web 실행

---

# 3️⃣ volumes

```yml
volumes:
  pgdata:
```

👉 DB 데이터를 저장하는 공간 정의

---

# 🔥 핵심 포인트 정리 (중요)

이 yml에서 중요한 개념 5개만 뽑으면👇

### 1. 서비스 이름 = 내부 DNS

```yml
db
```

👉 `localhost`가 아니라 `db`로 접근해야 함

---

### 2. depends_on + healthcheck

👉 "DB 준비 → API 실행" 보장

---

### 3. build vs image

* db → image (이미 존재)
* api/web → build (직접 만듦)

---

### 4. env 기본값 패턴

```yml
${VAR:-default}
```

👉 환경변수 없으면 default 사용

---

### 5. 프론트/백엔드 분리 구조

* web → 사용자
* api → 로직
* db → 데이터

---

# 🚀 이 구조의 장점

* 한 줄로 전체 실행 가능
* 환경 통일 (내 PC = 서버)
* CI/CD 연결 쉬움
* 라즈베리파이에도 그대로 배포 가능

---

# ⚠️ 주의할 점

```yml
POSTGRES_PASSWORD: postgres
SESSION_SECRET: change-me
```

👉 이건 **개발용 설정**
실서비스에서는 반드시 변경해야 함