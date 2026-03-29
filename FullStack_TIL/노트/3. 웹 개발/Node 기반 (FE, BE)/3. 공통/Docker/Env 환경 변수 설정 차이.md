
도커(docker)에서 환경변수를 설정하는 곳은 ==2 곳==이 있습니다.
1) 각 이미지 빌드를 담당하는 `Dockerfile`
2) 전체 컨테이너를 담당하는 `docker-copmose`

둘 다 컨테이너의 환경변수를 지정할 수 있기 때문에 혼동이 오는데,
중요한 것은 ==어느 시점에 환경변수를 넣느냐, 누가 더 우위에 있냐== 입니다!


* **Dockerfile의 `ENV` → 이미지 빌드 시 기본값**
* **docker-compose의 `environment` → 컨테이너 실행 시 주입 (덮어쓰기 가능)**

---

# 🧱 1. Dockerfile의 `ENV`

```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
```

### 특징

* **이미지에 “기본 환경변수”를 박아버림**
* `docker build` 할 때 포함됨
* 이 이미지로 만든 컨테이너는 기본적으로 이 값을 가짐

### 느낌

👉 “이 앱은 원래 PORT=3000으로 동작해요”

---

# 🚀 2. docker-compose의 `environment`

```yaml
services:
  app:
    environment:
      - PORT=5000
      - NODE_ENV=development
```

### 특징

* **컨테이너 실행할 때 주입**
* Dockerfile의 `ENV`를 **덮어쓸 수 있음**
* 환경별 설정 분리할 때 핵심

### 느낌

👉 “이번 실행에서는 PORT=5000으로 바꿔서 써”

---

# ⚔️ 우선순위 (중요)

둘 다 있으면?

👉 **docker-compose가 이김**

```
docker-compose environment > Dockerfile ENV
```

---

# 💡 실제 예시

### Dockerfile

```dockerfile
ENV PORT=3000
```

### docker-compose.yml

```yaml
environment:
  - PORT=5000
```

👉 결과:

```
컨테이너 내부 PORT = 5000
```

---

# 🤔 언제 뭐 써야 할까?

## Dockerfile ENV → “기본값”

* 앱이 최소한 돌아가기 위한 값
* 예: PORT, NODE_ENV 기본값

## docker-compose environment → “환경별 설정”

* 개발 / 테스트 / 운영 다르게
* API URL, DB 주소, CORS 등

---

# 🔥 실무 패턴 (중요)

### 1. Dockerfile

```dockerfile
ENV NODE_ENV=production
```

### 2. docker-compose

```yaml
environment:
  - NODE_ENV=development
  - DATABASE_URL=postgres://db:5432/app
```

👉 이렇게 하면

* 기본은 production
* 로컬에서는 development로 override

---

# 💣 추가로 알아두면 좋은 것

## ARG vs ENV 차이

```dockerfile
ARG API_URL
ENV API_URL=$API_URL
```

* `ARG` → **빌드 타임 전용**
* `ENV` → **컨테이너 실행 시 사용 가능**

---

# 🔚 정리

| 구분   | Dockerfile ENV | docker-compose environment |
| ---- | -------------- | -------------------------- |
| 시점   | 빌드 시           | 실행 시                       |
| 역할   | 기본값            | 덮어쓰기                       |
| 범위   | 이미지            | 컨테이너                       |
| 우선순위 | 낮음             | 높음                         |
