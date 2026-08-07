---
layout: post
slug: linux-system-monitor
title: "[2026-08-04] linux-system-monitor"
date: 2026-08-04 11:30:00 +0900
tags: [Linux, ShellScript, Security, Hardening, Codyssey]
category: Codyssey-Mission
---

# 🛡️ [2026-08-04] linux-system-monitor 미션 기술 딥다이브

운영 중인 리눅스 서버에서 가장 무서운 것은 **"갑작스러운 서버 다운"** 과 **"외부 침입 공격"** 입니다.

이번 **`linux-system-monitor`** 프로젝트의 핵심 쟁점은 **"현업 엔지니어링 운영 프로세스를 모델링하여, 서버 보안 요새화(Hardening)와 자동화 관제 파이프라인을 셸 스크립트로 어떻게 구현할 것인가?"** 였습니다. 💻

---

## ⚡ 쟁점 1: SSH 22번 포트 무차별 대입 공격 방어 ➡️ 서버 요새화 (Hardening)

인터넷에 연결된 서버의 `/var/log/auth.log`를 보면 무차별 대입 공격(Brute-force) 로그가 쉴 새 없이 찍힙니다.

### 💡 공학적 해결책: SSH 포트 변경 & Root 접근 차단 & UFW 화이트리스트

```bash
# 1. SSH 설정 변경 (/etc/ssh/sshd_config)
Port 20022              # 기본 22번 포트 대신 비표준 20022 포트로 변경
PermitRootLogin no      # Root 계정의 직접 원격 로그인 원천 차단!

# 2. UFW 방화벽 접근 제어 (Firewall Hardening)
sudo ufw default deny incoming # 기본 모든 인바운드 차단
sudo ufw allow 20022/tcp       # 인가된 포트만 허용
sudo ufw enable
```
- 비표준 포트로 변경하는 것만으로도 자동화 스캔 봇 공격의 95% 이상을 무력화할 수 있습니다!

---

## ⚡ 쟁점 2: 디스크 용량 고갈(Disk Full) 방지 ➡️ Log Rotate 자동화 파이프라인

관제 시스템에서 로그가 무한히 쌓여 디스크 용량이 100%가 되면 시스템이 멈춰 버립니다.

### 💡 공학적 해결책: `log_rotate_archive.sh` 스크립트 구축

매일 밤 지정된 시각에 로그 파일을 검사하여, 오래된 로그는 `.tar.gz`로 압축 보관하고 보관 기한(30일)이 지난 압축 파일은 자동 삭제하는 백업 회전 파이프라인을 구축했습니다.

---

## ⚡ 쟁점 3: 실시간 자원 모니터링 와치독 (`monitor.sh`)

CPU, 메모리, 디스크 사용량을 동적 계산하여 임계치(80%) 초과 시 운영자에게 알림 로그를 남기는 관제 스크립트를 작성했습니다:

```bash
#!/bin/bash
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
MEM_USAGE=$(free -m | awk 'NR==2{printf "%.2f", $3*100/$2 }')
DISK_USAGE=$(df -h / | awk 'NR==2{print $5}' | tr -d '%')

if (( $(echo "$CPU_USAGE > 80.0" | bc -l) )); then
    echo "[WARNING] CPU usage spike detected: ${CPU_USAGE}%" >> /var/log/sys_monitor_alert.log
fi
```

---

## 📝 요약 및 성과

서버 요새화(Hardening), UFW 접근 제어, 로그 회전 자동화, 자원 관제 스크립트까지 운영 엔지니어의 필수 안정화 파이프라인을 완성했습니다! 🚀
