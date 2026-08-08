---
layout: post
title: "💰 [python-budget-app] 4계층 SoC 서비스 아키텍처, 제너레이터 스트리밍 & 원자적 쓰기"
slug: python-budget-app
date: 2026-08-05 14:00:00 +0900
tags: [Python, OOP, Streaming, Architecture, AtomicWrite, Codyssey]
category: Codyssey-Mission
---

# 💰 python-budget-app 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

콘솔 기반 가계부 프로그램에서 흔히 발생하는 문제는 대용량 거래 내역 파일 저장 시 전체 데이터를 메모리에 다 불러와 OOM이 발생하는 문제와, 파일 저장 도중 전원 차단 시 기존 데이터 파일이 0바이트로 파손되는 현상입니다.

**`python-budget-app`** 미션에서는 CLI, Service, Repository, Model의 **4계층 SoC 아키텍처**를 적용하고 파이썬 `Generator` 기반 라인 단위 스트리밍 처리 및 **원자적 파일 쓰기(Atomic Write)** 패턴을 적용했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ CLI Presentation Layer (cli.py) ]
               │
               ▼
[ Service Layer (services.py) ] ── Decorators (@handle_exceptions, @log_execution)
               │
               ▼
[ Repository Data Layer (repositories.py) ] ── Generator Streaming (read_all)
               │
               ▼
[ Atomic Write Strategy ] ── Write Temp File ──> Atomic Rename (replace)
```

### 쟁점 1: 전체 파일 메모리 로딩 vs Generator 스트리밍

- 전체 `list`로 인메모리 로딩 시 데이터 증가에 비례하여 RAM 낭비.
- **`Generator` (yield)** 방식 적용으로 O(1)의 일정한 메모리 공간 복잡도로 파일 탐색 및 필터링 가능.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 `services.py` - 제너레이터 스트리밍 삭제 로직

```python
@handle_exceptions
@log_execution
def remove_category(self, name: str) -> None:
    name = name.strip()
    
    found = False
    def filter_category() -> Generator[Dict[str, Any], None, None]:
        nonlocal found
        for row in self.category_repo.read_all():
            if row['name'] == name:
                found = True
                continue
            yield row # 메모리 낭비 없이 1건씩 반환

    self.category_repo.rewrite_all(filter_category())
```

---

## 🧪 4. 테스트 & 무결성 검증

`pytest` 유닛 테스트 수트를 작성하여 카테고리 추가/삭제, 예산 초과 계산 알고리즘 및 파일 저장 무결성을 100% 검증했습니다.

---

## 📝 5. 결론 및 공학적 인사이트

- **안정적 아키텍처**: 4계층 분리로 유지보수성 극대화.
- **메모리 최적화**: Generator 활용으로 대용량 파일 데이터 스트리밍 처리 정복 🚀
