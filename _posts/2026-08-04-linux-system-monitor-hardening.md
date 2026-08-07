---
layout: post
title: "🛡️ SSH 포트 22번을 닫아라! 리눅스 관제 자동화 & 서버 요새화(Hardening) 실전기"
date: 2026-08-04 11:30:00 +0900
tags: [Linux, ShellScript, Security, Hardening, Codyssey]
category: Codyssey-Mission
---

운영 중인 리눅스 서버에 `/var/log/auth.log` 를 열어보신 적 있으신가요?  
`Failed password for invalid user root from ...` 로그가 초당 몇 번씩 수없이 찍히는 것을 보면 가슴이 서늘해집니다.

이번 **`linux-system-monitor`** 과제에서는 서버를 외부 공격으로부터 요새화(Hardening)하고, 자원 관제 및 로그 자동 회전(Log Rotate) 파이프라인을 셸 스크립트로 구축했습니다! 💻

---

### 🔒 서버 요새화 3단계 전략

#### 1단계: SSH 기본 포트 변경 (22 ➡️ 20022)
자동화 스캔 봇들은 99% 22번 포트만 노립니다. 비표준 포트로 변경하는 것만으로도 무차별 대입 공격의 90% 이상을 방어할 수 있습니다.
```bash
# /etc/ssh/sshd_config 수정
Port 20022
PermitRootLogin no  # Root 직접 로그인 철저 차단!
```

#### 2단계: UFW 방화벽 화이트리스트 설정
```bash
sudo ufw default deny incoming  # 기본 모든 인바운드 차단
sudo ufw allow 20022/tcp        # 변경된 SSH 포트 허용
sudo ufw enable
```

#### 3단계: 로그 자동 회전 파이프라인 (`log_rotate_archive.sh`)
로그 파일이 디스크를 가득 채워 서버가 다운되는 불상사를 막기 위해, 매일 밤 로그를 자동으로 압축하고 보관 기한이 지난 오래된 로그를 삭제하는 셸 스크립트를 작성했습니다.

---

### 📊 실시간 시스템 관제 셸 스크립트 (`monitor.sh`)

CPU, 메모리, 디스크 사용량을 주기적으로 체크하여 임계치(80%)를 초과하면 경고 로그를 남기도록 제작했습니다.

```bash
#!/bin/bash
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
MEM_USAGE=$(free -m | awk 'NR==2{printf "%.2f", $3*100/$2 }')
DISK_USAGE=$(df -h / | awk 'NR==2{print $5}' | tr -d '%')

echo "[$(date)] CPU: ${CPU_USAGE}% | MEM: ${MEM_USAGE}% | DISK: ${DISK_USAGE}%"
```

서버 운영이란 단순히 코드를 배포하는 데서 끝나는 것이 아니라, **"서버가 안정적으로 숨 쉴 수 있는 환경을 만들어 주는 것"**임을 체감한 훌륭한 실습이었습니다! 👍
