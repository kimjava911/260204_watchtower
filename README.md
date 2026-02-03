# Watchtower 자동 배포 예제 프로젝트

이 프로젝트는 Spring Boot 웹 애플리케이션을 Docker 환경에서 실행하고, **Watchtower**를 사용하여 새로운 버전이 배포될 때마다 자동으로 컨테이너를 업데이트하는 예제입니다.

## 🚀 프로젝트 구성

- **Spring Boot**: 현재 버전을 반환하는 간단한 REST API (`/`)를 제공합니다.
- **Docker (Multi-stage)**: 빌드와 실행 환경을 분리하여 최적화된 이미지를 생성합니다.
- **GitHub Actions**: `main` 브랜치에 푸시하면 자동으로 빌드하여 GHCR(GitHub Container Registry)에 이미지를 업로드합니다.
- **Docker Compose**: 애플리케이션과 Watchtower 서비스를 함께 실행합니다.
- **Watchtower**: 최신 이미지를 감지하여 컨테이너를 자동으로 재시작 및 업데이트합니다.

## 🛠️ 시작하기

### 1. 환경 설정
`.env.example` 파일을 복사하여 `.env` 파일을 생성하고 본인의 설정에 맞게 수정합니다.

```bash
cp .env.example .env
```

- `GITHUB_REPOSITORY_OWNER`: 본인의 GitHub 사용자명을 입력합니다.
- `WATCHTOWER_INTERVAL`: 업데이트 확인 주기(초)를 설정합니다.

### 2. 실행
Docker Compose를 사용하여 서비스를 실행합니다.

```bash
docker-compose up -d
```

실행 후 `http://localhost:8080`에 접속하면 현재 설정된 버전을 확인할 수 있습니다.

## 🔄 자동 업데이트 프로세스

1. **코드 수정**: `application.yaml`에서 `app.version`을 수정하고 GitHub `main` 브랜치에 푸시합니다.
2. **CI/CD**: GitHub Actions가 동작하여 새 이미지를 빌드하고 `ghcr.io`에 푸시합니다.
3. **감지**: 서버에서 실행 중인 **Watchtower**가 설정된 주기마다 레지스트리의 이미지와 현재 실행 중인 이미지를 비교합니다.
4. **업데이트**: 새 이미지가 발견되면 Watchtower가 기존 컨테이너를 중지하고, 새 이미지를 다운로드하여 컨테이너를 자동으로 다시 시작합니다.

## 🧐 Watchtower란?

**Watchtower**는 실행 중인 Docker 컨테이너의 베이스 이미지가 변경되었는지 감시하는 오픈소스 도구입니다. 

- **자동화**: 개발자가 수동으로 `docker pull` 및 `docker-compose up`을 할 필요가 없습니다.
- **효율성**: `--cleanup` 옵션을 통해 업데이트 후 남은 오래된 이미지를 자동으로 삭제하여 디스크 공간을 관리합니다.
- **유연성**: 특정 컨테이너만 감시하거나, 알림(Slack, Email 등)을 보내는 등 다양한 설정이 가능합니다.

## 📁 주요 파일 설명

- `src/main/resources/application.yaml`: 애플리케이션의 버전 정보가 하드코딩되어 있습니다.
- `Dockerfile`: Gradle 빌드 및 실행을 위한 멀티스테이징 설정입니다.
- `docker-compose.yml`: 앱과 Watchtower의 실행 정의서입니다.
- `.github/workflows/docker-publish.yml`: GHCR 배포를 위한 자동화 스크립트입니다.
