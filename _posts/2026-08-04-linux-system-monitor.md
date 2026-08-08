---
layout: post
title: "🛡️ [linux-system-monitor] 리눅스 시스템 관제 자동화, POSIX Awk & logrotate 파이프라인"
slug: linux-system-monitor
date: 2026-08-04 15:00:00 +0900
tags: [Linux, Shell, Awk, Security, Monitoring, CI-CD, Codyssey]
category: Codyssey-Mission
---

# 🛡️ linux-system-monitor 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

서버 관제 시스템은 CPU, 메모리, 디스크 사용량이 임계치를 초과할 때 즉시 알림을 발생시키고, 누적되는 로그 파일이 디스크를 가득 채워 서버가 가동 중단되는 OOM/Disk Full 장애를 사전에 방지해야 합니다.

**`linux-system-monitor`** 미션에서는 POSIX 표준 호환 Awk 파싱 스크립트와 보안 요새화(SSH 포트 변경, UFW 방화벽), 자동 logrotate 로그 압축 회전 파이프라인, GitHub Actions CI/CD 검증 수트를 구축했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ System Metrics (top, free, df) ] ──> [ monitor.sh (Awk Engine) ] ── Threshold Exceeded ──> Alert Log
                                                 │
[ Cron Job (Minutely) ] <────────────────────────┘
                                                 │
                                     [ log_rotate_archive.sh ] ── Gzip ──> Archive Directory
```

### 쟁점 1: POSIX Awk 호환성 및 이종 리눅스 환경 대응

- GNU Awk 전용 구문(`and()`, `length()` 특수함수) 사용 시 BSD/MacOS 또는 경량 Alpine Linux 환경에서 스크립트 파싱 오류 발생.
- **해결**: 모든 스크립트를 POSIX 표준 Awk 구문으로 작성하여 이종 OS 배포 안정성 확보.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 `monitor.sh` - 관제 파이프라인 코어

```bash
#!/bin/sh
set -eu

CPU_USAGE=$(top -bn1 | awk '/Cpu\(s\)/ {print 100 - $8}')
MEM_USAGE=$(free | awk '/Mem:/ {printf "%.2f", $3/$2 * 100}')
DISK_USAGE=$(df -h / | awk 'NR==2 {gsub("%",""); print $5}')

echo "[$(date '+%Y-%m-%d %H:%M:%S')] CPU: ${CPU_USAGE}% | MEM: ${MEM_USAGE}% | DISK: ${DISK_USAGE}%"

if [ $(echo "$CPU_USAGE > 80.0" | bc) -eq 1 ]; then
    echo "🚨 [ALERT] CPU 사용량 경고: ${CPU_USAGE}%" >> /var/log/system_alerts.log
fi
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **자동화 검증 수트**: `.github/workflows/test.yml`을 통해 CI/CD 환경에서 임계치 초과 알림 및 로그 압축 로직 테스트 자동 수행.
2. **매뉴얼 작성**: 초보자용 9개 파트의 `manual.md` 교재 및 `DEMO_MANUAL_1_1.md` 작성 완수.

---

## 📝 5. 결론 및 공학적 인사이트

- **안정적 관제**: POSIX 표준 준수로 운영체제 제약 없는 견고한 시스템 관제 구현.
- **장애 방지**: logrotate 압축 회전을 통한 시스템 디스크 고갈 예방 🚀
