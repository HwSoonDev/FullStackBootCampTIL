
일반적으로 환경 변수는 docker-compose.yml이 마지막으로 컨테이너에 박아 넣어 주지만,
Next.js의 경우, 빌드 시점에 env 파일이 박힙니다!
그래서 반드시! Dockerfile(빌드 담당)에서 compose의 환경 변수를 받아 와주어야 합니다 
- `( $ )` 사용
## Dockerfile에서 ARG + ENV (프론트 필수)

Next.js 기준 👇

```dockerfile
ARG NEXT_PUBLIC_API_URL  
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL
```


그리고 compose에서:

```yml
build:  
  args:  
    NEXT_PUBLIC_API_URL: http://192.168.0.10:3001
```

👉 이걸 해야 하는 이유:

- Next.js는 **빌드 시점에 env가 박힘**
- 그냥 environment만 쓰면 **프론트에 반영 안됨**