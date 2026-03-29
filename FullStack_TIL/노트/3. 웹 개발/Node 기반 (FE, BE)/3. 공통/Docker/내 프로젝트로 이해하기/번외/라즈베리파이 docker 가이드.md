
라즈베리파이는 일반적인 리눅스와는 성격이 조금 다르기 때문에,
가이드를 따로 정리했습니다.

## 설치 방법

라즈베리파이에 Docker를 설치하는 과정은 생각보다 단순하지만, 몇 가지 포인트만 정확히 짚으면 훨씬 안정적으로 사용할 수 있습니다. 특히 ARM 아키텍처라서 일반 PC와 다르다는 점을 이해하는 것이 중요합니다.

## 기본 설치 방법 (공식 방식)

라즈베리파이에서는 가장 안전하고 간단한 방법이 Docker 공식 설치 스크립트를 사용하는 것입니다.

먼저 시스템을 최신 상태로 업데이트합니다.

```
sudo apt update
sudo apt upgrade -y
```

그 다음 Docker를 설치합니다.

```
curl -fsSL https://get.docker.com | sh
```

이 한 줄로 Docker 엔진이 자동 설치됩니다.

설치가 끝나면 Docker 서비스를 실행합니다.

```
sudo systemctl start docker
sudo systemctl enable docker
```

## 권한 설정 (중요)

기본적으로 Docker는 root 권한이 필요해서 매번 sudo를 붙여야 합니다. 이를 피하려면 사용자에게 권한을 추가합니다.

```
sudo usermod -aG docker $USER
```

이후 **로그아웃 후 다시 로그인**해야 적용됩니다.

## 정상 설치 확인

```
docker --version
docker run hello-world
```

여기서 hello-world가 실행되면 정상입니다.

## Docker Compose 설치

요즘은 별도로 설치하지 않고 Docker에 포함된 형태로 사용합니다.

```
docker compose version
```

만약 없다면 아래로 설치 가능합니다.

```
sudo apt install docker-compose-plugin
```

## 라즈베리파이에서 중요한 포인트

라즈베리파이는 일반 PC와 다르게 ARM 기반이라서 여기서 문제가 자주 발생합니다.

* 이미지가 ARM을 지원해야 함
* 일부 이미지(x86 전용)는 실행 불가

예를 들어:

* `postgres:16-alpine` → ARM 지원 (사용 가능)
* 일부 오래된 이미지 → 실행 안 됨

## 자주 겪는 문제

설치 자체보다 실제 사용에서 막히는 경우가 많습니다.

* 컨테이너는 뜨는데 접속이 안 됨 → 포트 문제
* 같은 네트워크인데 접근 안 됨 → 방화벽 / 공유기 설정
* 빌드 느림 → 라즈베리 성능 한계
