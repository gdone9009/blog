---
layout: post
title: "☁️ 내 서버는 내가 지킨다! AWS VPC 사설 격리 네트워크 및 보안 그룹 구축기"
slug: cloud-infra-aws
date: 2026-08-03 14:00:00 +0900
tags: [AWS, VPC, Security, Cloud, Codyssey]
category: Codyssey-Mission
---

# ☁️ cloud-infra-aws 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

클라우드(AWS)에서가장 범하는 위험한 실수는 AWS 기본 제공 Default VPC에 EC2 인스턴스를 생성하고 퍼블릭 IP를 붙인 채 방치하는 것입니다.  
인터넷 전체에 노출된 서버는 생성 후 단 몇 분 만에 자동화된 봇들의 SSH 무차별 대입 스캔(Brute-force Scan) 대상이 됩니다.

**`cloud-infra-aws`** 미션에서는 퍼블릭 클라우드 환경에서 **완벽히 사설 격리된 가상 네트워크(VPC)** 를 설계하고, 최소 권한 원칙(Principle of Least Privilege)을 준수하는 인프라 아키텍처를 구축했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: Default VPC vs 독자 사설 격리 VPC 아키텍처

```text
[ Internet Gateway (IGW) ]
            │
    [ Public Route Table (0.0.0.0/0 -> igw) ]
            │
    [ Public Subnet (10.0.1.0/24) ]
            │
    [ Security Group (Inbound WhiteList) ]
            │
    [ EC2 Instance (Web App) ]
```

1. **VPC (`10.0.0.0/16`)**: 65,536개의 사설 IP 대역을 갖는 독립 네트워크 공간 분리
2. **Public Subnet (`10.0.1.0/24`)**: 외부 웹 트래픽 수용 전방 서브넷
3. **Internet Gateway (IGW)**: 사설망과 외부 인터넷 간 유일한 트래픽 관문 연결

---

### 쟁점 2: IAM 최소 권한 원칙 vs 보안 그룹 (Security Group) 인바운드 방화벽

#### 💡 Security Group 인바운드 화이트리스트 정책

- `HTTP (80)`: 웹 서비스 용도로 전 세계 수용 (`0.0.0.0/0`)
- `SSH (22 / 20022)`: 관리 목적이므로 **오직 개발자 PC의 단일 IP(`My-IP/32`)만 허용**

#### 💡 IAM 사용자 정책 JSON 예시 (`policy.json`)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "arn:aws:ec2:ap-northeast-2:*:instance/*"
    }
  ]
}
```

---

## 📝 4. 결론 및 성과

- **VPC 격리 네트워크 구축**: 퍼블릭 IP 남발 방지 및 사설 서브넷 망 분리
- **2중 보안 장벽**: IAM 최소 권한 및 보안 그룹 IP 화이트리스트로 침입 사전 차단 🚀
