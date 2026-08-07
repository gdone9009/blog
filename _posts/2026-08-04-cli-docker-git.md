---
layout: post
title: "🐳 개발자의 필수 무기! Linux CLI, Docker 컨테이너 및 Git 협업 구축기 (cli-docker-git)"
slug: cli-docker-git
date: 2026-08-04 16:00:00 +0900
tags: [Docker, Linux, Git, Nginx, Codyssey]
category: Codyssey-Mission
---

# 🐳 cli-docker-git 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

"내 컴퓨터에서는 분명 잘 되는데, 왜 서버나 동료 컴퓨터에서는 실행이 안 되지?"  
이 고전적인 환경 의존성 문제는 Docker 컨테이너 기술의 등장으로 해결되었습니다.

**`cli-docker-git`** 미션에서는 Linux CLI, 커스텀 Dockerfile 빌드, 바인드 마운트 기반 데이터 영속성 제어 및 Git 브랜치 전략을 실습했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: Custom Dockerfile 작성 & Nginx 컨테이너 가상화

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 쟁점 2: 바인드 마운트 (`-v`) 데이터 실시간 동기화

컨테이너 삭제 시 데이터 손실을 방지하고 로컬 개발 코드 변경 사항을 즉시 반영하기 위해 바인드 마운트를 적용했습니다:

```bash
docker run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html --name dev-web-server nginx:alpine
```

---

## 📝 4. 결론 및 성과

- **재현 가능한 개발 환경**: Docker 컨테이너화로 환경 격리 달성
- **개발 생산성**: 바인드 마운트를 통한 코드 수정 즉시 반영 🚀
