# 🥝 Home Server DevOps Template

미니PC, 시놀로지 등의 홈서버에 **Docker 기반 프로젝트를 배포**하기 위한 CI/CD 스캐폴딩 Repository Template입니다.
Dockerfile만 있으면 어떤 언어/프레임워크든 사용할 수 있습니다.

## 🛠️ Feature

- **Issue Templates**: 기능 추가, 기능 개선, 버그 리포트 이슈 템플릿
- **Issue Labels**: 이슈 할당에 알맞은 라벨과 자동 연동 워크플로우
- **Github Action Workflow**: 자체 서버에 배포할 수 있는 자동화된 CI/CD 워크플로우

## 📋 사전 요구사항

- **홈 서버**: Docker 설치 완료, SSH 접속 가능
- **DockerHub**: 계정 및 액세스 토큰 발급
- **프로젝트**: 루트에 `Dockerfile` 존재

## 🔄 배포 흐름

```
Push to Branch
     │
     ▼
GitHub Actions (Build)
     │  ├─ deploy.env 설정 로드 및 검증
     │  ├─ Docker 이미지 빌드
     │  └─ DockerHub에 이미지 Push
     ▼
GitHub Actions (Deploy)
     │  ├─ SSH로 홈 서버 접속
     │  ├─ 새 이미지 Pull
     │  ├─ 기존 컨테이너 Graceful Shutdown
     │  ├─ 새 컨테이너 실행
     │  └─ Health Check
     ▼
  배포 완료 (+ Discord 알림)
```

## 🚀 Quick Start

1. 화면 상단의 `Use this template`을 클릭합니다.
2. 아래 **Setup** 섹션을 따라 GitHub Secrets를 설정합니다.
3. 코드를 push하면 자동으로 빌드 및 배포가 실행됩니다.

## ⚙️ Setup

이 템플릿을 사용하기 전 아래 사항들을 수동 설정해야 합니다.

### GitHub Secrets 설정

| 변수명 | 설명 |
|--------|------|
| `DOCKERHUB_USERNAME` | DockerHub 사용자명 |
| `DOCKERHUB_TOKEN` | DockerHub 액세스 토큰 |
| `SERVER_HOST` | 배포 대상 서버 IP/도메인 |
| `SERVER_USER` | 서버 SSH 접속 사용자명 |
| `SERVER_SSH_KEY` | 서버 SSH 개인키 |
| `SERVER_PORT` | 서버 SSH 접속 포트 |
| `PROJECT_ENV` | 프로젝트 `.env` 파일 전체 내용 (선택) |
| `DEPLOY_ENV` | 배포 세부 설정 (하단 참고) |

### `DEPLOY_ENV` 설정

`example_deploy.env` 파일을 참고하여 작성합니다.

| 변수명 | 설명 | 예시 |
|--------|------|------|
| `DEPLOY_MODE` | 배포 모드 (`single` 또는 `compose`, 기본값: `single`) | `single` |
| `PROJECT_NAME` | 프로젝트 이름 (DockerHub 이미지명, 컨테이너명) | `my-app` |
| `PROJECT_BASE_PATH` | 서버 내 프로젝트 기본 경로 | `/home/user/apps` |
| `PROJECT_FOLDER_NAME` | 프로젝트 폴더 이름 | `my-app` |
| `PROJECT_SUB_PATH` | 프로젝트 하위 경로 | `data` |
| `CONTAINER_PORT` | 컨테이너 내부 포트 (기본값: 8000) | `8000` |
| `PRODUCTION_PORT` | 프로덕션 환경 호스트 포트 | `8000` |
| `DEVELOPMENT_PORT` | 개발 환경 호스트 포트 | `8001` |
| `TEST_PORT` | 테스트 환경 호스트 포트 | `8002` |

> 볼륨 마운트 경로는 `{PROJECT_BASE_PATH}/{PROJECT_FOLDER_NAME}/{PROJECT_SUB_PATH}` 형태로 자동 구성됩니다.

## 🔧 선택 설정

### Discord 배포 알림

배포 성공/실패 시 Discord 알림을 받으려면 GitHub Secrets에 `DISCORD_WEBHOOK_URL`을 추가하세요.
설정하지 않으면 알림 단계가 자동으로 건너뛰어집니다.

### Docker Compose 배포 모드

멀티 서비스 배포가 필요한 경우 Docker Compose 모드를 사용할 수 있습니다.

1. `DEPLOY_ENV`에서 `DEPLOY_MODE=compose`로 설정합니다.
2. 프로젝트 루트에 `docker-compose.yml`을 작성합니다. (`example_docker-compose.yml` 참고)
3. Compose 파일 내에서 아래 변수들을 사용할 수 있습니다:

| 변수명 | 설명 |
|--------|------|
| `DOCKER_IMAGE` | DockerHub 이미지 전체 경로 (자동 설정) |
| `HOST_PORT` | 브랜치별 호스트 포트 (자동 설정) |
| `CONTAINER_PORT` | 컨테이너 내부 포트 (자동 설정) |
| `HOST_MOUNT_PATH` | 호스트 마운트 경로 (자동 설정) |
| `CONTAINER_MOUNT_PATH` | 컨테이너 마운트 경로 (자동 설정) |

> Compose 모드에서도 빌드는 동일하게 DockerHub를 통해 진행됩니다.
> `PROJECT_ENV`에 설정한 앱 환경 변수는 `.env` 파일에 자동으로 포함됩니다.

### 컨테이너 리소스 제한

필요에 따라 워크플로우의 `docker run` 명령에 리소스 제한 옵션을 직접 추가할 수 있습니다:

```bash
docker run -d \
  --memory 512m \
  --cpus 1.0 \
  ...
```

## 🔍 트러블슈팅

### 포트 충돌

> `Error: port is already allocated`

다른 컨테이너나 프로세스가 같은 포트를 사용 중입니다. `DEPLOY_ENV`의 포트 설정을 확인하세요.

```bash
# 서버에서 포트 사용 현황 확인
sudo lsof -i :8000
```

### SSH 접속 실패

> `ssh: connect to host ... port ...: Connection refused`

- `SERVER_HOST`, `SERVER_PORT`, `SERVER_USER` 값을 확인하세요.
- `SERVER_SSH_KEY`에 개인키 전체(헤더/푸터 포함)가 입력되었는지 확인하세요.
- 서버의 방화벽에서 SSH 포트가 허용되어 있는지 확인하세요.

### 이미지 Pull 실패

> `Error: pull access denied` 또는 `manifest unknown`

- `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN` 값을 확인하세요.
- DockerHub 토큰에 Read/Write 권한이 있는지 확인하세요.

### 컨테이너 시작 실패

> `❌ 컨테이너가 시작되지 않았습니다`

워크플로우 로그에 출력된 컨테이너 로그를 확인하거나, 서버에서 직접 확인하세요:

```bash
docker logs <컨테이너명>
```

## 🛞 수동 배포

워크플로우 실패 시 서버에서 직접 배포할 수 있습니다:

```bash
# 1. 이미지 Pull
docker pull <dockerhub-username>/<project-name>:<branch>

# 2. 기존 컨테이너 정리
docker stop <container-name> && docker rm <container-name>

# 3. 새 컨테이너 실행
docker run -d \
  --restart unless-stopped \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  -p <host-port>:<container-port> \
  --name <container-name> \
  --env-file .env \
  -e TZ=Asia/Seoul \
  -v /etc/localtime:/etc/localtime:ro \
  -v <host-mount-path>:<container-mount-path> \
  <dockerhub-username>/<project-name>:<branch>
```