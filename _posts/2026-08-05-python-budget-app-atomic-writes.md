---
layout: post
title: "💰 파이썬 순수 표준 라이브러리로 튼튼한 파일 가계부(Atomic Write) 만들기"
date: 2026-08-05 16:20:00 +0900
tags: [Python, Architecture, FileIO, AtomicWrite, Codyssey]
category: Codyssey Mission
---

데이터베이스(DB)나 외부 ORM 프레임워크가 없는 환경에서, 파일(CSV)에 데이터를 저장하다가 갑자기 프로그램이 꺼지면 어떻게 될까요?  
파일이 쓰여지다 말고 뚝 끊겨서 전체 데이터 파일이 깨질 수 있습니다! 😱

이번 **`python-budget-app`** 미션은 외부 라이브러리를 일절 사용하지 않고, 파이썬 표준 라이브러리만으로 계층화 아키텍처와 **원자적 쓰기(Atomic Write)**를 구현한 파일 가계부 서비스 개발기입니다.

---

### 🏗️ 4계층 아키텍처 (Model-Repository-Service-CLI)

코드의 유지보수성을 극대화하기 위해 관심사 분리(SoC) 원칙에 따라 계층을 엄격히 나눴습니다:

- **Model (`models.py`)**: 거래 내역(`Transaction`), 예산(`Budget`) 데이터 클래스 정의
- **Repository (`repositories.py`)**: CSV 파일 데이터 읽기/쓰기 및 원자적 쓰기 처리
- **Service (`services.py`)**: 수입/지출 합계 계산, 카테고리별 예산 잔액 검증 로직
- **CLI (`cli.py`)**: 사용자와 대화하는 콘솔 인터페이스

---

### 🛡️ 데이터 훼손 방지를 위한 Atomic Write (원자적 쓰기) 메커니즘

파일에 데이터를 쓸 때 원본 파일에 직접 덮어쓰지 않고, **임시 파일(`.tmp`)에 먼저 기록한 후 원자적 이동(`os.replace`)**을 수행합니다.

```python
import os

def save_transactions_atomically(filepath, data):
    temp_filepath = filepath + '.tmp'
    # 1. 임시 파일에 데이터 쓰기
    with open(temp_filepath, 'w', encoding='utf-8') as f:
        f.write(data)
    
    # 2. 원자적(Atomic) 교체 - OS 차원에서 파일 교체가 즉시 완료됨
    os.replace(temp_filepath, filepath)
```

이렇게 하면 파일 쓰기 도중에 시스템 전원이 차단되더라도 원본 파일이 손상되지 않고 완전한 안전성을 보장받습니다!

파이썬 기본 라이브러리의 깊은 기능들과 아키텍처 모듈화의 중요성을 제대로 체득할 수 있었던 즐거운 프로젝트였습니다. 🎈
