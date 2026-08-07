---
layout: post
title: "💰 파이썬 순수 표준 라이브러리로 튼튼한 파일 가계부(Atomic Write) 만들기"
slug: python-budget-app
date: 2026-08-05 16:20:00 +0900
tags: [Python, Architecture, FileIO, AtomicWrite, Codyssey]
category: Codyssey-Mission
---

# 💰 python-budget-app 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

순수 파일(CSV/JSON) 입출력을 수행할 때 가장 무서운 사고는 **"파일을 쓰는 도중 프로그램 장애나 전원 차단이 발생하여 원본 파일이 0바이트로 파손되는 현상"** 입니다.

**`python-budget-app`** 미션에서는 외부 프레임워크 없이 표준 라이브러리만으로 4계층 아키텍처(SoC)를 구성하고, 데이터 파손을 방지하는 **원자적 쓰기(Atomic Write)** 기법을 도입했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: 관심사 분리(SoC) ➡️ Model-Repository-Service-CLI 4계층 구조

- **Model (`models.py`)**: `Transaction`, `Budget` 가치 객체
- **Repository (`repositories.py`)**: 파일 쓰기 및 원자적 교체 처리
- **Service (`services.py`)**: 비즈니스 예산 초과 검증
- **CLI (`cli.py`)**: 사용자 콘솔 인터페이스

---

### 쟁점 2: 원자적 쓰기 (Atomic Write) 메커니즘

```python
import os

def save_atomically(filepath: str, content: str):
    temp_filepath = filepath + ".tmp"
    
    # 1. 임시 파일(.tmp)에 완전히 작성 후 커널 버퍼 디스크 동기화
    with open(temp_filepath, "w", encoding="utf-8") as f:
        f.write(content)
        f.flush()
        os.fsync(f.fileno()) # 커널 버퍼 강제 물리 저장

    # 2. OS 차원 원자적(Atomic) 파일 교체 (순간적인 교체로 0바이트 손상 방지)
    os.replace(temp_filepath, filepath)
```

---

## 📝 4. 결론 및 성과

- **데이터 무결성**: OS 레벨 원자적 교체로 파일 손상 위험 차단
- **계층화 아키텍처**: 표준 라이브러리 기반 4계층 모듈화 성공 🚀
