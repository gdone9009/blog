---
layout: post
title: "🛡️ SSH 포트 22번을 닫아라! 리눅스 관제 자동화 & 서버 요새화(Hardening) 실전기"
slug: linux-system-monitor
date: 2026-08-04 11:30:00 +0900
tags: [Linux, ShellScript, Security, Hardening, Codyssey]
category: Codyssey-Mission
---

# 🛡️ linux-system-monitor 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

리눅스 운영 서버의 가장 큰 위협은 **"SSH 22번 포트를 통한 자동화 스캔 봇의 무차별 대입 공격"** 과 **"디스크 로그 누적으로 인한 용량 고갈(Disk Full) 장애"** 입니다.

**`linux-system-monitor`** 미션에서는 서버 요새화(Hardening) 및 자원 모니터링, 로그 자동 회전(Log Rotate) 파이프라인을 셸 스크립트로 구축했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: SSH 20022 포트 변경 & Root 로그인 원천 차단

```bash
# /etc/ssh/sshd_config 파일 요새화 설정
Port 20022              # 비표준 포트 지정 (자동 스캔 95% 무력화)
PermitRootLogin no      # Root 직접 원격 로그인 차단
MaxAuthTries 3          # 인증 시도 3회 제한
```

### 쟁점 2: Log Rotate 자동화 (`log_rotate_archive.sh`)

```bash
#!/bin/bash
LOG_DIR="/var/log/sys_monitor"
ARCHIVE_DIR="/var/log/sys_monitor/archive"
mkdir -p "$ARCHIVE_DIR"

# 7일 지난 로그 tar.gz 압축 보관
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec tar -czf "$ARCHIVE_DIR/log_$(date +%Y%m%d_%H%M%S).tar.gz" {} \;
# 30일 지난 압축 파일 자동 삭제
find "$ARCHIVE_DIR" -type f -name "*.tar.gz" -mtime +30 -delete
```

---

## 📝 4. 결론 및 성과

- **서버 요새화**: 비표준 포트 변경 및 UFW 접근 제어로 외부 공격 방어
- **운영 자동화**: 자원 감시 와치독 및 디스크 풀 방지 로그 회전 체계 구축 🚀
