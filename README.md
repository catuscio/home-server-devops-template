# 🥝Home Server FastAPI DevOps Template
미니PC, 시놀로지 등의 홈서버에서 배포하는 FastAPI 프로젝트의 초기 Github 설정 및 CI/CD 설정을 포함하고 있는 Repository Template 입니다.

## 🛠️Feature
- **Issue Templates**: 프로젝트에 꼭 필요한 기능 추가, 버그 리포트 이슈 템플릿
- **Issue Labels**: 이슈 할당에 알맞은 라벨과 자동 연동 워크플로우
- **Github Action workflow**: 자체 서버에 배포할 수 있는 자동화 된 워크플로우

## 🚀Quick Start
화면 상단의 `Use this template`을 클릭합니다.


## ⚙️Setup
이 템플릿을 사용하기 전 아래 사항들을 수동 설정해야 합니다.

#### Github Secrets 설정
| 변수명 | 설명 |
|-------------|------|
| DOCKERHUB_USERNAME | DockerHub 사용자명 |
| DOCKERHUB_TOKEN | DockerHub 액세스 토큰 |
| SERVER_HOST | 배포 대상 서버 IP/도메인 |
| SERVER_USER | 서버 SSH 접속 사용자명 |
| SERVER_PASSWORD | 서버 SSH 접속 비밀번호 |
| ENV | .env 파일 전체 내용 복사본 |

#### `.github/config/deploy.env` 설정

| 변수명 | 설명 |
|--------|------|
| PROJECT_NAME | 프로젝트 이름 |
| PROJECT_BASE_PATH | 서버 내 프로젝트 기본 경로 |
| PROJECT_FOLDER_NAME | 프로젝트 폴더 이름 |
| PROJECT_SUB_PATH | 프로젝트 하위 경로 |
| PRODUCTION_PORT | 프로덕션 환경 포트 |
| DEVELOPMENT_PORT | 개발 환경 포트 |
| TEST_PORT | 테스트 환경 포트 |
