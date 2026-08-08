---
layout: post
title: "☁️ [cloud-infra-aws] AWS VPC 사설 격리 네트워크 아키텍처 및 IAM 최소 권한 구축기"
slug: cloud-infra-aws
date: 2026-08-03 14:00:00 +0900
tags: [AWS, VPC, Security, Cloud, IAM, Codyssey]
category: Codyssey-Mission
---

# ☁️ cloud-infra-aws 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

클라우드(AWS) 환경에서 흔히 범하는 심각한 보안 오류는 AWS가 기본 제공하는 Default VPC의 퍼블릭 서브넷에 EC2 인스턴스를 배치하고 0.0.0.0/0 접속을 허용하는 것입니다. 인터넷에 노출된 서버는 수분 내에 봇들의 SSH 무차별 대입 공격 대상이 됩니다.

**`cloud-infra-aws`** 미션에서는 3계층 네트워크 아키텍처 기반의 **독자적인 VPC 사설망**을 구축하고, 최소 권한 원칙(Principle of Least Privilege)을 준수하는 IAM 및 보안 그룹 화이트리스트 정책을 수립했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ Internet Gateway (IGW) ]
            │
[ Public Route Table (0.0.0.0/0 -> IGW) ]
            │
[ Public Subnet (10.0.1.0/24) ] ── EC2 Web Server (HTTP 80 Allow All)
            │
[ Security Group (Inbound Rule) ] ── SSH (Port 22/20022): My-IP/32 Only
```

### 쟁점 1: Default VPC 사용 vs 독자 사설 VPC 설계

- **Default VPC**: 기본 설정으로 간편하지만, 모든 인스턴스에 퍼블릭 IP가 할당되어 인터넷 노출 위험 가중.
- **독자 사설 VPC (`10.0.0.0/16`)**: 사설 IP 65,536개 대역의 격리망을 생성하고 Public/Private Subnet을 엄격히 분리.

### 쟁점 2: Security Group 화이트리스트 정책

- `HTTP (80)`: 외부 대민 웹 서비스를 위해 전 세계 수용 (`0.0.0.0/0`).
- `SSH (22 / 20022)`: 관리 목적의 관문이므로 **개발자 단일 IP (`My-IP/32`)로 엄격 차단**.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 최소 권한 IAM 정책 규격 (`policy.json`)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowEC2ManagementOnly",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:RebootInstances"
      ],
      "Resource": "arn:aws:ec2:ap-northeast-2:*:instance/*"
    }
  ]
}
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **외부 SSH 접속 검증**: 허용되지 않은 외부 IP에서 인스턴스로 SSH 접근 시 커넥션 타임아웃(Connection Timed Out)으로 침입 완전 차단 확인.
2. **HTTP 웹 서비스 검증**: 80번 포트로 접근 시 Nginx 웹 응답이 정상 반환됨을 확인.

---

## 📝 5. 결론 및 공학적 인사이트

- **Defense in Depth**: IAM 권한 분리와 Security Group 네트워크 방화벽의 2중 보안 적용.
- **클라우드 보안 태세 강화**: 격리된 VPC 구축을 통한 보안 사고 사전 예방 🚀
