---
layout: post
title: "🧠 NPU는 왜 AI 연산에 빠를까? 파이썬으로 직접 만드는 NPU MAC 연산 시뮬레이터"
slug: mini-npu-simulator
date: 2026-08-06 13:10:00 +0900
tags: [AI, NPU, Python, MatrixMultiplication, Hardware, Codyssey]
category: Codyssey-Mission
---

# 🧠 mini-npu-simulator 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

딥러닝 및 인공지능 시대에 NPU(Neural Processing Unit)가 주목받는 이유는 AI 연산의 90% 이상을 차지하는 행렬 곱 연산을 일반 CPU보다 압도적인 병렬 속도로 처리하기 때문입니다.

**`mini-npu-simulator`** 미션에서는 NPU 연산의 근간인 MAC(Multiply-Accumulate) 엔진을 시뮬레이션하고, 2D 배열 1D Flattening 메모리 최적화 성능을 분석했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: MAC(Multiply-Accumulate) 연산 원리

- MAC 연산식: $Y = (A 	imes B) + C$
- CPU는 명령어 디코딩과 분기 제어 오버헤드가 큰 반면, NPU 연산 엔진은 곱셈과 누적을 1클락 만에 동시 수행합니다.

### 쟁점 2: 파이썬 2D 리스트 캐시 미스 ➡️ 1D Flattening 메모리 최적화

```python
# 2차원 리스트를 1차원 연속 메모리 구조로 직렬화하여 CPU 캐시 히트율 극대화
def flatten_matrix(matrix_2d):
    return [val for row in matrix_2d for val in row]

def mac_engine_1d(arr_a, arr_b, length):
    acc = 0.0
    for i in range(length):
        acc += arr_a[i] * arr_b[i] # 캐시 친화적 연속 메모리 접근
    return acc
```

---

## 📝 4. 결론 및 성과

- **AI 하드웨어 이해**: MAC 전용 연산 엔진 원리 체득
- **메모리 최적화**: 1D Flattening 적용으로 캐시 히트율 및 연산 속도 향상 🚀
