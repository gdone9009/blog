---
layout: post
title: "💻 [cli-docker-git] 개발 워크스테이션 환경 구축, Custom Dockerfile & Git Workflow"
slug: cli-docker-git
date: 2026-08-04 11:00:00 +0900
tags: [Docker, Git, Linux, CLI, Workstation, Codyssey]
category: Codyssey-Mission
---

# 💻 cli-docker-git 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

"내 컴퓨터에서는 잘 되는데 운영 서버에서는 왜 안 되지?"는 소프트웨어 개발 현장의 고질적인 병목입니다.

**`cli-docker-git`** 미션은 컨테이너 가상화(Docker) 기술과 버전 관리 시스템(Git), 리눅스 CLI 환경을 통합하여 개발 호스트 환경에 의존하지 않는 동일한 표준 개발 워크스테이션 환경을 자동화 구축하는 기술 파이프라인입니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ Developer Local Machine ]
            │ (git push)
            ▼
[ GitHub Repository ]
            │ (git pull / CI/CD)
            ▼
[ Isolated Docker Container ] ── (Port Binding -p 8080:80) ──> Web User
```

### 쟁점 1: Nginx 403 Forbidden 버그 트러블슈팅

Docker 인프라에 커스텀 `index.html`을 바인드 마운트 할 때 호스트 파일 권한 미비로 Nginx에서 `403 Forbidden` 에러가 발생하는 이슈 분석 및 해결.
- **원인**: 호스트 리눅스의 파일 소유권 권한(600) 부족으로 컨테이너 내부 `www-data` 유저의 읽기 거부.
- **해결**: `chmod 644 index.html` 및 Dockerfile 레이어 권한 명시 설정.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 최적화 Custom `Dockerfile`

```dockerfile
FROM nginx:alpine

# 권한 및 설정 복사
COPY index.html /usr/share/nginx/html/index.html
RUN chmod 644 /usr/share/nginx/html/index.html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **Docker 컨테이너 빌드 및 실행**: `docker build -t dev-workstation .` 및 `docker run -d -p 8080:80 dev-workstation` 정상 구동.
2. **Curl 검증**: `curl -I http://localhost:8080` 실행 시 `HTTP/1.1 200 OK` 확인.

---

## 📝 5. 결론 및 공학적 인사이트

- **재현 가능 환경**: Docker를 통한 완벽한 실행 환경 표준화 달성.
- **실전 트러블슈팅**: 권한 문제 해결을 통한 리눅스 퍼미션 매커니즘 정복 🚀
