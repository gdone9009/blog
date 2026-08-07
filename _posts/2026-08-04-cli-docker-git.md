---
layout: post
slug: cli-docker-git
title: "[2026-08-04] cli-docker-git"
date: 2026-08-04 16:00:00 +0900
tags: [Docker, Linux, Git, Nginx, Codyssey]
category: Codyssey-Mission
---

# 🐳 [2026-08-04] cli-docker-git 미션 기술 딥다이브

개발자들의 오래된 딜레마 중 하나: **"내 컴퓨터에서는 잘 실행되는데, 왜 서버나 동료 컴퓨터에서는 안 되지?"** 🤔

이번 **`cli-docker-git`** 미션의 핵심 쟁점은 **"터미널 CLI, Docker 컨테이너 기술 및 Git 버전 관리를 조합하여 언제 어디서나 즉시 재현 가능한(Reproducible) 고성능 개발 환경을 어떻게 구축할 것인가?"** 였습니다.

---

## ⚡ 쟁점 1: 환경 의존성 문제 해결 ➡️ Custom Dockerfile 작성 & Nginx 웹 서버 컨테이너화

운영체제 버전, 라이브러리 충돌 문제를 해결하기 위해 Docker 컨테이너 가상화를 도입했습니다.

### 💡 공학적 해결책: 경량 커스텀 Dockerfile 빌드

```dockerfile
# Nginx Alpine 기본 이미지 활용
FROM nginx:alpine

# 커스텀 웹 소스 및 설정 파일 복사
COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
- 단 한 줄의 `docker run` 명령어로 모든 개발자가 100% 동일한 Nginx 환경을 3초 만에 띄울 수 있게 되었습니다!

---

## ⚡ 쟁점 2: 컨테이너 휘발성 방지 ➡️ 바인드 마운트(Bind Mount) vs 볼륨 영속성

Docker 컨테이너는 삭제되면 내부 데이터가 모두 사라지는 휘발성(Ephemeral)을 가집니다.

### 💡 공학적 해결책: 바인드 마운트 `-v` & 포트 매핑 `-p`

- **바인드 마운트 (`-v`)**: 개발자 PC의 로컬 코드 디렉터리를 컨테이너 내부 웹 경로와 실시간 동기화하여, 코드를 수정하면 컨테이너 재빌드 없이 웹 화면에 즉시 반영
- **포트 매핑 (`-p 8080:80`)**: 호스트 PC의 8080 포트를 컨테이너 80 포트와 연결하여 외부 접속 통로 확보

```bash
docker run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html --name my-web-server nginx:alpine
```

---

## 📝 요약 및 성과

CLI 기초 조작부터 시작하여 Docker 이미지 빌드, 컨테이너 데이터 영속성 제어, Git/GitHub 커밋 연동에 이르는 재현 가능 개발 인프라 구축 능력을 완전히 습득했습니다! 🚀
