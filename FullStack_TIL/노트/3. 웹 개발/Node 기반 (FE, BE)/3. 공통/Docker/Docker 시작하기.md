
## Docker를 설치

## Docker를 사용해보자

![[Pasted image 20260329115243.png]]

Docker가 무사히 설치되었다면 터미널(or CMD)에서 Docker 명령어를 입력했을 때, 정상적으로 응답이 옵니다. 

이미 만들 프로젝트가 있다면 전체적인 흐름은 아래와 같습니다.
```
프로젝트 → Dockerfile 작성 → 이미지 빌드 → 컨테이너 실행
```

먼저 프로젝트에 필요한 `Dockerfile`을 작성합니다.
`Dockerfile`은 주로 빌드, 배포를 위한 명령어를 모아두는 파일입니다.

프로젝트 루트에 작성 하는데, 
프론트 백이 함께 있는 프로젝트를 예시로 들면,

```
project/
 ├── docker-compose.yml   ← 여기 (전체 관리)
 ├── frontend/
 │    ├── Dockerfile      ← 프론트용
 │    ├── .dockerignore
 │    └── ...
 ├── backend/
 │    ├── Dockerfile      ← 백엔드용
 │    ├── .dockerignore
 │    └── ...
```

이런 형태가 됩니다.
- `docker-compose.yml`
- `Dockerfile`
- `.dockerignore`

모두 작성해 주었다면, yml 파일이 있는 프로젝트 루트에서 

```
docker compose up --build
```

도커 컨테이너가 빌드 됩니다. (백그라운드 빌드 실행은 `docker compose up -d`)

