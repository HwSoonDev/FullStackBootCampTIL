
ssl을 설정하다 보니, nginx 폴더의 구성이 다음처럼 바뀌었습니다.

![[Pasted image 20260329190610.png]]

| 파일 이름                  | 설명                       |
| ---------------------- | ------------------------ |
| `proxy-locations.conf` | 프론트와 백의 경로를 지시           |
| `default.conf`         | nginx의 기본 설정             |
| `ssl.conf`             | 인증서를 관리하는 certbot을 위한 설정 |
이라고 합니다.


인증서가 없을 때도 nginx가 떠야 해서 ssl 관련 설정을 분리했다고 하는데,
아직 공부가 더 필요해 보입니다 ;;

### 왜 `default.conf`와 `ssl.conf`로 나뉘었나

1. 인증서가 없을 때도 nginx가 떠야 해서  
    Let’s Encrypt webroot 방식이면 먼저 80번으로 nginx가 떠 있어야 `/.well-known/acme-challenge/`를 줄 수 있습니다.  
    그런데 443 + `ssl_certificate` 가 들어간 설정을 처음부터 같이 켜 두면, 아직 `fullchain.pem`이 없을 때 nginx가 설정 읽는 단계에서 실패할 수 있습니다.  
    그래서 80만 담은 `default.conf`는 항상 쓰고, 443·인증서는 `ssl.conf`로 나중에(인증서 발급 후) 붙이는 식으로 나눈 겁니다.
    
2. HTTPS 켤 때만 볼륨 한 줄을 켜면 되게  
    `docker-compose.yml`에서 `ssl.conf` 마운트를 주석 해제하면 그때부터 HTTPS 서버 블록이 생깁니다. 같은 파일 안에서 주석 달고 지우기보다 운영이 단순해집니다.
    
3. 역할 분리
    
    - `default.conf`: HTTP(80), ACME 챌린지
    - `ssl.conf`: TLS(443), 인증서 경로

### 어떻게 “연결”되나

nginx는 `/etc/nginx/conf.d/` 아래의 `.conf` 파일을 각각 읽어서 합칩니다.  
`default.conf`가 `ssl.conf`를 `include`하는 구조가 아니라, 둘 다 같은 디렉터리에 있으면 둘 다 로드되는 관계입니다.

- `default.conf` → `listen 80` 인 `server { ... }` 하나
- `ssl.conf` → `listen 443 ssl` 인 `server { ... }` 하나 (마운트했을 때만)

요청이 들어오면 포트·`server_name` 등에 맞는 `server` 블록 하나가 선택됩니다. 80으로 오면 첫 번째, 443으로 오면 두 번째가 처리합니다.

### 공통 부분은 어떻게 맞추나

둘 다 안에서 `include /etc/nginx/includes/proxy-locations.conf;` 를 쓰므로, `/api`·`/` 프록시 규칙은 한 파일만 고치면 HTTP/HTTPS가 같이 따라갑니다.  
즉 연결 고리는 “서로 include”가 아니라 “같은 `proxy-locations`를 각각 include” 입니다.

한 줄 요약:  
나눈 이유는 인증서 없이도 80으로 먼저 띄우기 + HTTPS는 준비되면 볼륨만 추가하기 위해서이고, 연결은 nginx가 `conf.d`의 여러 `.conf`를 한 설정으로 합치는 방식이며, 프록시 동작은 `includes/proxy-locations.conf`를 둘 다 include해서 맞춥니다.