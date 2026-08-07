---
layout: post
title: "🚀 Welcome to gdone9009's Tech Odyssey | 깊이 있는 공학적 호기심과 기록의 여정"
date: 2026-08-01 09:00:00 +0900
tags: [Welcome, Manifesto, Codyssey, Engineering]
category: General
---

# 👋 안녕하세요! 소프트웨어 엔지니어 gdone9009입니다.

> **"Code is easy. Engineering the underlying system is art."**  
> *"단순히 프레임워크를 '사용'하는 개발자를 넘어, 시스템의 근간과 메모리, 하드웨어 연산 원리를 깊게 탐구합니다."*

---

## 🌟 블로그 개설 취지 (The Why)

개발을 공부하다 보면 우리는 수많은 편리한 프레임워크(React, Spring, Redis, Docker, AWS...)를 만납니다. 버튼 몇 번, 명령어 몇 줄로 거대한 시스템이 움직이죠. 

하지만 어느 순간 문득 가슴속에서 질문이 솟구쳤습니다:

* ❓ *"Redis의 dict는 대체 메모리 한계(Memory Limit)가 왔을 때 어떻게 O(1) 속도로 오래된 키를 격리(Eviction)시킬까?"*
* ❓ *"NPU 같은 AI 하드웨어 가속기는 왜 2차원 데이터 처리 시 Memory Flattening을 하면 속도가 미친 듯이 빨라질까?"*
* ❓ *"외부 브라우저에서 서버를 향해 들어오는 무차별 패킷 스캔을 AWS VPC 사설망과 방화벽으로 어떻게 철통 방어할까?"*

이 기술 블로그 **`gdone9009.log`** 는 이러한 **엔지니어링적 호기심을 직접 코드로 검증하고, 실패와 트러블슈팅(Troubleshooting) 경험을 기록하기 위한 공간**입니다.

---

## 🗺️ Codyssey 10대 공학 미션 맵 (Core Missions)

본 블로그에서는 제가 수립하고 완수한 **10대 핵심 공학 미션**의 비하인드 스토리와 기술 딥다이브를 친근하고 위트 있게 공유합니다:

| 카테고리 | 미션 및 프로젝트명 | 기술 스택 | 핵심 주제 & 딥다이브 포인트 |
|---|---|---|---|
| **⚡ Low-level Engine** | **mini-redis** | `Python`, `Doubly Linked List`, `Min Heap` | 파이썬 `dict` 없이 이중 연결 리스트 & LRU Eviction 직접 구현 |
| **🧠 AI Hardware** | **mini-npu-simulator** | `Python`, `Linear Algebra` | NPU MAC(Multiply-Accumulate) 연산 및 2D Flattening 최적화 |
| **🛡️ Security & OS** | **linux-system-monitor** | `Shell Script`, `Linux`, `UFW` | SSH 20022 포트 변경, 서버 요새화(Hardening) & 관제 스크립트 |
| **☁️ Cloud Infra** | **cloud-infra-aws** | `AWS VPC`, `EC2`, `IAM` | VPC 사설 격리망, IGW 라우팅 & 보안 그룹 방화벽 프로비저닝 |
| **💰 Clean Code** | **python-budget-app** | `Python Standard Library`, `pytest` | 계층화 아키텍처(Model-Repo-Service) & 원자적 쓰기(Atomic Write) |
| **🎨 Frontend Architecture**| **vanilla-js-portfolio** | `Vanilla JS (ES6+)`, `HTML5`, `CSS3` | React 없이 구축하는 단방향 상태 관리 (Event -> State -> Render) |
| **🗄️ Database** | **sql-db** | `SQL`, `SQLite`, `Python` | 1:N 참조 무결성(PK/FK) 제약조건 & 도서 관리 시스템 분석 쿼리 |

---

## 🎨 블로그 글을 읽는 3가지 꿀팁!

1. ☕ **커피 한 잔과 함께 가볍게!**: 어려운 시스템 용어도 최대한 비유와 그림으로 쉬운 이야기처럼 적었습니다.
2. 💻 **코드 보드**: 실제 문제 해결에 쓰였던 핵심 파이썬/셸/JS 소스 코드가 가감 없이 수록되어 있습니다.
3. 🔗 **포트폴리오와의 연동**: 포스팅 상단의 프로젝트 링크를 누르면 [gdone9009 대표 포트폴리오 웹사이트](https://gdone9009.github.io/)로 이동하여 실제 동작 모습을 확인하실 수 있습니다.

---

## 💌 마무리하며

> *"혼자 가면 빠르게 가지만, 기록하며 나누면 멀리 간다."*

제 글이 컴퓨터 공학 기초 원리를 탐구하거나, 시스템 구축 중 버그와 싸우고 계신 많은 동료 개발자분들께 작게나마 영감과 도움이 되기를 바랍니다.

궁금한 점, 건의 사항, 기술적 이견은 언제든지 댓글이나 이메일(`gdone9009@github.com`)로 남겨주세요!  
감사합니다. 즐거운 탐험 되세요! 🎈
