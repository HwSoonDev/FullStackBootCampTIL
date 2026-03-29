
```dockerfile
FROM node:22-bookworm-slim AS builder
WORKDIR /app

COPY package.json package-lock.json* ./
RUN npm ci

COPY . .

ARG NEXT_PUBLIC_API_URL=http://localhost:3001
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL

RUN npm run build

FROM node:22-bookworm-slim AS runner
WORKDIR /app

ENV NODE_ENV=production

COPY --from=builder /app/package.json ./
COPY --from=builder /app/package-lock.json* ./
RUN npm ci --omit=dev

COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/next.config.ts ./
COPY --from=builder /app/postcss.config.mjs ./
COPY --from=builder /app/tsconfig.json ./

EXPOSE 3000
CMD ["npx", "next", "start", "-H", "0.0.0.0", "-p", "3000"]
```

한 줄 씩 이해해보자!

# 🔥 1단계: builder (빌드 전용 컨테이너)

```dockerfile
FROM node:22-bookworm-slim AS builder
```

👉 Node.js 22 기반 이미지 사용  
👉 `bookworm-slim` = 가벼운 Debian 기반  
👉 `AS builder` = 이 단계를 “builder”라는 이름으로 저장 (나중에 재사용)

---

```dockerfile
WORKDIR /app
```

👉 컨테이너 내부 작업 폴더 설정  
👉 이후 모든 명령은 `/app` 기준

---

```dockerfile
COPY package.json package-lock.json* ./
```

👉 의존성 파일만 먼저 복사  
👉 `package-lock.json*` = 없어도 에러 안 나게

💡 이유:

- 코드보다 먼저 복사 → Docker 캐시 활용 (속도 ↑)

---

```dockerfile
RUN npm ci
```

👉 의존성 설치 (정확한 버전으로)

💡 `npm install` vs `npm ci`

- `ci` = 더 빠르고, lock 파일 기준으로 정확히 설치 (Docker에서 권장)

---

```dockerfile
COPY . .
```

👉 나머지 전체 코드 복사

---

```dockerfile
ARG NEXT_PUBLIC_API_URL=http://localhost:3001
```

👉 빌드 시 사용할 변수 정의

💡 특징:

- `docker build` 할 때만 사용 가능
- 런타임에서는 없음

---

```dockerfile
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL
```

👉 위 ARG 값을 실제 환경 변수로 등록

💡 Next.js 특징:

- `NEXT_PUBLIC_` 붙으면 프론트 코드에서도 사용됨

---

```dockerfile
RUN npm run build
```

👉 Next.js 빌드 실행

결과:

```text
.next/
```

👉 이 폴더가 실제 실행 파일

---

# 🔥 2단계: runner (실행 전용 컨테이너)

```dockerfile
FROM node:22-bookworm-slim AS runner
```

👉 다시 깨끗한 새 컨테이너 시작

💡 이유:

- 빌드에 필요 없는 파일 제거 → 이미지 용량 ↓

---

```dockerfile
WORKDIR /app
```

👉 동일하게 작업 폴더 설정

---

```dockerfile
ENV NODE_ENV=production
```

👉 프로덕션 모드 설정

효과:

- 성능 최적화
- dev dependency 제외 가능

---

```dockerfile
COPY --from=builder /app/package.json ./
COPY --from=builder /app/package-lock.json* ./
```

👉 builder에서 package 정보만 가져옴

---

```dockerfile
RUN npm ci --omit=dev
```

👉 **운영에 필요한 패키지만 설치**

💡 핵심:

- devDependencies 제거 → 용량 ↓, 보안 ↑

---

```dockerfile
COPY --from=builder /app/.next ./.next
```

👉 빌드 결과 복사 (핵심)

---

```dockerfile
COPY --from=builder /app/public ./public
```

👉 정적 파일 복사 (이미지, favicon 등)

---

```dockerfile
COPY --from=builder /app/next.config.ts ./
COPY --from=builder /app/postcss.config.mjs ./
COPY --from=builder /app/tsconfig.json ./
```

👉 Next 실행에 필요한 설정 파일 복사

💡 왜 필요?

- 일부 설정은 런타임에도 사용됨

---

```dockerfile
EXPOSE 3000
```

👉 컨테이너가 사용하는 포트 명시

(실제 열리는 건 아님, 문서용 느낌)

---

```dockerfile
CMD ["npx", "next", "start", "-H", "0.0.0.0", "-p", "3000"]
```

👉 컨테이너 실행 시 명령어

설명:

- `next start` → 빌드된 앱 실행
- `-H 0.0.0.0` → 외부 접속 허용 (중요)
- `-p 3000` → 포트 지정

---

# 🧠 전체 흐름 한방 정리

```text
[builder]
코드 복사 → npm install → build

[runner]
필요한 것만 복사 → 가볍게 실행
```

---

# 🔥 이 Dockerfile의 핵심 장점

### ✅ 멀티 스테이지 빌드

- 빌드용 / 실행용 분리
- 이미지 용량 ↓

### ✅ 캐시 최적화

- package.json 먼저 복사

### ✅ 보안 + 성능

- devDependencies 제거

---

# ⚠️ 네가 꼭 알아야 할 포인트

## ❗ NEXT_PUBLIC_API_URL

```dockerfile
ARG NEXT_PUBLIC_API_URL=http://localhost:3001
```

👉 이거 문제될 가능성 있음

왜냐면:

- Docker 안에서는 `localhost` = 자기 자신

👉 보통 이렇게 바꿔야 함:

```text
http://backend:3001
```

(= docker-compose 서비스 이름)


# `COPY` 명령어의 의미와 순서의 중요성

# 🔥 한 줄 핵심

👉 `COPY` = **“내 컴퓨터 파일을 컨테이너 안으로 넣는 명령어”**

---

# 🧱 왜 필요하냐?

Docker 컨테이너는 기본적으로:

👉 **완전히 빈 환경 + 베이스 이미지뿐**

즉:

* 네 코드 ❌ 없음
* package.json ❌ 없음
* 소스코드 ❌ 없음

👉 그래서 직접 넣어줘야 함 → `COPY`

---

# 🛠️ 기본 구조

```dockerfile
COPY <호스트 경로> <컨테이너 경로>
```

---

# 📦 예시 1 (가장 중요)

```dockerfile
COPY package.json ./
```

👉 의미:

* 내 컴퓨터의 `package.json`
* → 컨테이너 `/app` 폴더로 복사

---

# 📦 예시 2

```dockerfile
COPY . .
```

👉 의미:

* 현재 폴더 전체
* → 컨테이너로 복사

---

# 🧠 왜 두 번 나눠서 COPY 하냐?

너 Dockerfile 보면 이렇게 되어 있지:

```dockerfile
COPY package.json package-lock.json* ./
RUN npm ci
COPY . .
```

👉 이유 = **속도 최적화 (캐시)**

---

## ❌ 이렇게 하면 느림

```dockerfile
COPY . .
RUN npm install
```

👉 코드 하나 바뀌어도
→ npm install 다시 실행됨 (느림 💀)

---

## ✅ 이렇게 하면 빠름

```dockerfile
COPY package.json ./
RUN npm ci
COPY . .
```

👉 package.json 안 바뀌면
→ npm install 재사용 (빠름 ⚡)

---

# 📦 멀티 스테이지에서 COPY

이 부분도 중요 👇

```dockerfile
COPY --from=builder /app/.next ./.next
```

👉 의미:

* builder 컨테이너에서 만든 결과물을
* runner 컨테이너로 복사

💡 즉:
👉 “컨테이너 → 컨테이너 복사”

---

# ⚠️ COPY vs ADD

거의 항상:

👉 `COPY` 써라

`ADD`는:

* 압축 자동 해제
* URL 다운로드

👉 예측 어려움 → 실무에서 잘 안 씀

---

# 💡 진짜 중요한 포인트 3개

## 1. COPY는 빌드 시점에만 실행됨

* 컨테이너 실행 후에는 영향 없음

---

## 2. .dockerignore 영향 받음

```text
node_modules
.next
.git
```

👉 COPY할 때 제외됨

---

## 3. 경로 기준 헷갈리기 쉬움

```dockerfile
WORKDIR /app
COPY . .
```

👉 결과:

* `/app` 안에 복사됨

---

# 🔥 한 번에 이해하는 그림

```text
[내 컴퓨터]
  ├── package.json
  ├── src/

        ↓ COPY

[컨테이너]
  /app
   ├── package.json
   ├── src/
```



