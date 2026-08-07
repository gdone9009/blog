---
layout: post
title: "🛡️ SSH 포트 22번을 닫아라! 리눅스 관제 자동화 & 서버 요새화(Hardening) 실전기"
date: 2026-08-04 11:30:00 +0900
tags: [Linux, ShellScript, Security, Hardening, Codyssey]
category: Codyssey-Mission
---

리눅스 서버 관제 자동화 및 SSH/방화벽 보안 강화(Hardening) 프로젝트입니다! 💻

SSH 포트를 기본 22번에서 비표준 포트(20022)로 변경하고 Root 직접 로그인을 차단했습니다. 또한 UFW 방화벽 화이트리스트 접근 제어와 CPU/Memory 자원 관제 셸 스크립트(`monitor.sh`), 로그 자동 압축 파이프라인(`log_rotate_archive.sh`)을 구축했습니다.
