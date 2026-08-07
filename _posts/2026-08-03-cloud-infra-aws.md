---
layout: post
title: "☁️ 내 서버는 내가 지킨다! AWS VPC 사설 격리 네트워크 및 보안 그룹 구축기"
slug: cloud-infra-aws
date: 2026-08-03 14:00:00 +0900
tags: [AWS, VPC, Security, Cloud, Codyssey]
category: Codyssey-Mission
---

# ☁️ cloud-infra-aws 미션 기술 딥다이브

클라우드 가상 서버(EC2)를 구축할 때, 많은 사람들이 AWS 기본 제공 Default VPC에 EC2를 만들고 퍼블릭 IP를 붙인 채 방치하곤 합니다.  
하지만 이는 24시간 내내 전 세계 해커와 스캔 봇들에게 서버 문을 열어두는 것과 같습니다!

이번 **`cloud-infra-aws`** 미션의 핵심 쟁점은 **"AWS 퍼블릭 클라우드 생태계에서 완벽히 격리된 사설망(VPC) 인프라 아키텍처를 설계하고 보안 경계를 어떻게 확립할 것인가?"** 였습니다. 🛡️

---

## ⚡ 쟁점 1: Default VPC vs 독자 사설 격리 VPC 아키텍처 수립

Default VPC는 모든 서브넷이 인터넷과 직접 연결되어 보안에 취약합니다. 

### 💡 공학적 해결책: 3계층 네트워크 아키텍처 직접 프로비저닝

1. **VPC (`10.0.0.0/16`)**: 외부 네트워크와 분리된 독자 가상 사설망 공간 창조
2. **Public Subnet (`10.0.1.0/24`)**: 외부 웹 요청을 수용할 전방 배치 서브넷
3. **Internet Gateway (IGW) & Route Table**: 
   - `0.0.0.0/0` 인터넷 트래픽이 오직 지정된 패킷 라우팅 파이프라인(IGW)을 통해서만 통과하도록 통제

---

## ⚡ 쟁점 2: IAM 최소 권한 원칙 vs 보안 그룹(Security Group) 인바운드 방화벽

클라우드 자원에 대한 접근 통제(Access Control)는 인프라 보안의 핵심 쟁점입니다.

### 💡 공학적 해결책: 2중 방어벽 전략

- **IAM (Identity and Access Management)**: `AdministratorAccess` 같은 위험한 권한 대신, **최소 권한 원칙(Principle of Least Privilege)**을 준수하여 EC2 및 VPC 관리에 필요한 최소 IAM 사용자 정책만 주입
- **Security Group (보안 그룹)**:
  - `HTTP (80)`: 웹 서비스를 위해 전 세계 수용 (`0.0.0.0/0`)
  - `SSH (22)`: 관리 목적 접속이므로 **오직 지정된 내 개발 PC의 IP(`My-IP/32`)만 화이트리스트 지정**

```text
[Internet User] ---> (HTTP:80) ---> [Security Group] ---> [Public EC2]
[Hacker Bot]   ---> (SSH:22) ---> ❌ [Security Group Denied]
[My Admin PC]  ---> (SSH:22) ---> ✅ [Security Group Allowed]
```

---

## ⚡ 쟁점 3: 네트워크 접속 타임아웃 트러블슈팅 (Troubleshooting)

VPC와 EC2를 모두 만들었는데 외부에서 웹 페이지 접속이 되지 않는 트러블슈팅 상황이 발생했습니다!

- **원인 분석**: Subnet 생성 후 **Route Table(라우팅 테이블)에 인터넷 게이트웨이(IGW)로 향하는 라우팅 경로(`0.0.0.0/0 -> igw-xxx`) 추가를 누락**하여 패킷이 서브넷 밖으로 나가지 못함
- **해결**: 라우팅 테이블 명시적 바인딩 후 패킷 흐름(Packet Flow) 정상화 성공!

---

## 📝 요약 및 성과

단순한 클라우드 콘솔 조작을 넘어, VPC 격리 네트워크 통제, 라우터 패킷 경로 수립, 보안 그룹 화이트리스트 등 엔터프라이즈급 퍼블릭 클라우드 인프라 엔지니어링의 정수를 체득했습니다! 🚀
