---
layout: post
title: "🧠 [mini-npu-simulator] Mini NPU v1.5 하드웨어 행렬연산 시뮬레이션 & 1D Flattening 최적화"
slug: mini-npu-simulator
date: 2026-08-06 13:00:00 +0900
tags: [Python, NPU, Hardware, MatrixMultiplication, Cache, Codyssey]
category: Codyssey-Mission
---

# 🧠 mini-npu-simulator 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

GPU와 NPU(Neural Processing Unit)가 딥러닝 AI 연산에서 CPU 대비 수십~수백 배 빠른 핵심 이유는 행렬 곱셈(GEMM) 연산을 병렬 실행하는 MAC(Multiply-Accumulate) 연산 코어를 하드웨어 레이어에 대량 배치했기 때문입니다.

**`mini-npu-simulator`** 미션은 NPU의 MAC 연산 엔진과 데이터 입력 프로세스, 2차원 배열의 1차원 Flattening 메모리 연속성 캐시 최적화 알고리즘을 시뮬레이션 구현한 미션입니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ 2D Matrix (Row/Col pointer) ] ──> Dynamic Memory Jump (Cache Miss ↑)
                │
                ▼ (1D Flattening)
[ Continuous Array Memory ] ────> Sequential Fetch (Spatial Locality Cache Hit ↑)
```

### 쟁점 1: 2차원 리스트 이중 루프 vs 1차원 Flattened 배열 단일 루프

- **2차원 방식**: 행(`r`)과 열(`c`) 인덱스 계산 오버헤드 발생 및 메모리 파편화로 인한 캐시 미스 증가.
- **1차원 Flattening**: 메모리에 데이터가 연속 배치되므로 하드웨어 공간 지역성(Spatial Locality) 캐시 히트율 증가.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 `npu_engine.py` - MAC 연산 및 1D 메모리 최적화

```python
def calculate_mac_optimized(flat_pattern, flat_filter):
    score = 0.0
    for i in range(len(flat_pattern)):
        score += flat_pattern[i] * flat_filter[i]
    return score

def judge_with_epsilon(score_cross, score_x, epsilon=1e-9):
    diff = score_cross - score_x
    if abs(diff) < epsilon:
        return "UNDECIDED"
    return "Cross" if diff > 0 else "X"
```

---

## 🧪 4. 테스트 & 무결성 검증

`time.perf_counter()` 기반 벤치마킹 실행 결과, 1차원 Flattening 최적화 MAC 엔진이 기존 2차원 방식 대비 약 10% 내외의 Execution Time 단축 성과를 입증했습니다.

---

## 📝 5. 결론 및 공학적 인사이트

- **하드웨어 친화적 서술**: 메모리 주소 연속 배치와 캐시 락 구조의 중요성 파악.
- **부동소수점 오차 무결성**: Epsilon 판정 로직으로 수치적 안정성 확보 🚀
