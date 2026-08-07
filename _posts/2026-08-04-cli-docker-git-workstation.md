---
layout: post
title: "🐳 개발자의 필수 무기! Linux CLI, Docker 컨테이너 및 Git 협업 구축기 (cli-docker-git)"
date: 2026-08-04 16:00:00 +0900
tags: [Docker, Linux, Git, Nginx, Codyssey]
category: Codyssey-Mission
---

개발 워크스테이션 환경 구축을 위해 Linux CLI 터미널 조작, Docker 컨테이너 가상화 및 Git/GitHub 버전 관리를 결합한 실습 파이프라인 수립기입니다! 🐳

---

### 🛠️ 핵심 수행 내용

1. **Custom Dockerfile 작성**:
   - Nginx 커스텀 웹 서버 Dockerfile 작성 및 이미지 빌드
2. **컨테이너 바인드 마운트 & 볼륨 영속성**:
   - `-v` 옵션을 통해 호스트 환경의 파일 변경이 컨테이너 웹 서버에 즉시 반영되도록 구성
3. **포트 매핑 (Port Mapping)**:
   - `-p 8080:80` 포트 매핑을 통해 가상 환경 서비스 검증

어떤 환경에서도 한 줄의 명령어로 동일한 개발 환경을 복제하는 재현 가능(Reproducible) 인프라 구축의 중요성을 체득했습니다! 🚀
