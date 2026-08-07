---
layout: post
title: "💰 파이썬 순수 표준 라이브러리로 튼튼한 파일 가계부(Atomic Write) 만들기"
slug: python-budget-app
date: 2026-08-05 16:20:00 +0900
tags: [Python, Architecture, FileIO, AtomicWrite, Codyssey]
category: Codyssey-Mission
---

# 💰 python-budget-app 미션 기술 딥다이브

외부 DB나 ORM 프레임워크 없이 순수 파일(CSV)에 데이터를 저장할 때 발생하는 치명적인 위험:  
**"파일을 쓰는 도중 프로그램이 강제 종료되거나 전원이 꺼지면 기존 파일 전체가 0바이트로 깨진다!"**

이번 **`python-budget-app`** 미션의 핵심 쟁점은 **"파이썬 표준 라이브러리만으로 계층화 아키텍처를 설계하고, 원자적 쓰기(Atomic Write) 기법으로 데이터 손상을 어떻게 철저히 방지할 것인가?"** 였습니다. 💡

---

## ⚡ 쟁점 1: 프레임워크 없는 관심사 분리(SoC) ➡️ Model-Repository-Service-CLI 계층 아키텍처

단일 파이썬 파일에 모든 코드를 때려 넣으면 유지보수가 불가능해집니다.

### 💡 공학적 해결책: 4계층 아키텍처 준수

- **Model (`models.py`)**: `Transaction`, `Category`, `Budget` 가치 객체(VO) 및 타입 힌팅
- **Repository (`repositories.py`)**: CSV 파일 입출력 및 원자적 쓰기 제어
- **Service (`services.py`)**: 예산 초과 검증, 수입/지출 통계 분석 비즈니스 로직
- **CLI (`cli.py`)**: 사용자와 대화하는 인터페이스 분리

---

## ⚡ 쟁점 2: 파일 파손 방지를 위한 원자적 쓰기 (Atomic Write) 메커니즘

원본 파일 `budgets.csv`에 직접 `open(filepath, 'w')`로 파일 쓰기를 하면, 쓰기 도중 에러 발생 시 원본 데이터가 날아갑니다.

### 💡 공학적 해결책: 임시 파일 생성 후 OS 차원 `os.replace` 교체

```python
import os

def save_atomically(filepath, content):
    temp_filepath = filepath + ".tmp"
    
    # 1. 임시 파일(.tmp)에 데이터 완전 쓰기
    with open(temp_filepath, "w", encoding="utf-8") as f:
        f.write(content)
        f.flush()
        os.fsync(f.fileno()) # 커널 버퍼 데이터를 물리 디스크로 강제 플러시
        
    # 2. OS 레벨 원자적(Atomic) 교체 - 순간적으로 파일 교체가 수행되어 데이터 훼손 불가
    os.replace(temp_filepath, filepath)
```

---

## ⚡ 쟁점 3: 메모리 과다 사용 방지 ➡️ 제너레이터(Generator) 스트리밍

대용량 CSV 파일을 읽을 때 전체 데이터를 메모리에 다 올리는 대신 파이썬 제너레이터 `yield` 스트리밍 기법을 도입하여 메모리 사용량을 최소화했습니다.

---

## 📝 요약 및 성과

표준 라이브러리만으로 4계층 아키텍처 모듈화, 원자적 쓰기 데이터 무결성 보장, pytest 기반 자동화 테스트 수트까지 완벽히 구축해 냈습니다! 🚀
