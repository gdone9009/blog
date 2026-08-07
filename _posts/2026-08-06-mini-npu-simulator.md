---
layout: post
title: "🧠 NPU는 왜 AI 연산에 빠를까? 파이썬으로 직접 만드는 NPU MAC 연산 시뮬레이터"
slug: mini-npu-simulator
date: 2026-08-06 13:10:00 +0900
tags: [AI, NPU, Python, MatrixMultiplication, Hardware, Codyssey]
category: Codyssey-Mission
---

# 🧠 mini-npu-simulator 미션 기술 딥다이브

요즘 딥러닝과 AI 분야에서 CPU/GPU보다 훨씬 더 주목받는 칩이 있습니다. 바로 **NPU(Neural Processing Unit, 신경망 처리 장치)** 입니다!

이번 **`mini-npu-simulator`** 프로젝트의 핵심 쟁점은 **"NPU 연산의 근간인 MAC(Multiply-Accumulate) 엔진을 파이썬으로 시뮬레이션하고, 2D 배열 Flattening 최적화를 통해 메모리 접근 성능을 어떻게 증명할 것인가?"** 였습니다. ⚡

---

## ⚡ 쟁점 1: CPU vs NPU ➡️ MAC(Multiply-Accumulate) 연산의 원리

AI 신경망 연산의 90% 이상은 행렬 곱(Matrix Multiplication)과 누적 가산 연산입니다.

### 💡 MAC 연산 식: $Y = (A 	imes B) + C$

CPU는 일반적인 덧셈, 뺄셈, 조건문 명령어를 번갈아 처리하느라 행렬 곱에서 연산 병목이 발생하는 반면, NPU는 MAC 전용 연산기가 2차원 그리드로 배치되어 단 한 클락 만에 곱셈과 누적을 동시 처리합니다.

---

## ⚡ 쟁점 2: 파이썬 2D 리스트 캐시 미스 ➡️ 1D Flattening 메모리 직렬화 최적화

파이썬의 2차원 리스트 `[[...], [...]]` 는 요소들이 메모리상에 연속적으로 존재하지 않고 포인터 참조 구조를 가집니다. 따라서 CPU 캐시 미스(Cache Miss)가 빈번히 발생합니다.

### 💡 공학적 해결책: 1차원 Flattening 메모리 직렬화

```python
# 2차원 행렬을 1차원 연속 메모리 배열로 변환
def flatten_matrix(matrix_2d):
    return [val for row in matrix_2d for val in row]

# 1D 메모리 직렬화 기반 MAC 연산 엔진
def mac_engine_1d(array_a, array_b, length):
    accumulator = 0.0
    for i in range(length):
        accumulator += array_a[i] * array_b[i] # 캐시 친화적 연속 접근
    return accumulator
```

- 3x3부터 25x25 행렬까지 크기를 확장하며 벤치마크한 결과, **1D Flattening 적용 시 캐시 히트율(Cache Hit Rate) 상승으로 연산 시간이 획기적으로 단축됨**을 증명했습니다!

---

## ⚡ 쟁점 3: 부동소수점 수치 연산 정밀도 제어 (Epsilon Policy)

컴퓨터 부동소수점 연산에서 발생하는 $0.1 + 0.2 
eq 0.3$ 미세 오차 문제를 해결하기 위해 $Epsilon = 10^{-9}$ 오차 정밀도 검증 정책을 수립했습니다.

---

## 📝 요약 및 성과

AI 하드웨어 가속기 시뮬레이션을 통해 NPU 가속 원리 체득, 2D Flattening 메모리 레이아웃 최적화 및 수치 해석 연산 안정성을 완성했습니다! 🚀
