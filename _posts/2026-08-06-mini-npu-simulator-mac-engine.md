---
layout: post
title: "🧠 NPU는 왜 AI 연산에 빠를까? 파이썬으로 직접 만드는 NPU MAC 연산 시뮬레이터"
date: 2026-08-06 13:10:00 +0900
tags: [AI, NPU, Python, MatrixMultiplication, Hardware, Codyssey]
category: Codyssey-Mission
---

요즘 ChatGPT, LLaMA 같은 대규모 AI 모델이 주목받으면서 **NPU(Neural Processing Unit, 신경망 처리 장치)**가 핫 이슈입니다.  
CPU나 GPU와 달리 NPU는 왜 딥러닝 연산에서 엄청난 속도와 효율을 낼까요?

그 답은 바로 AI 연산의 90% 이상을 차지하는 **MAC(Multiply-Accumulate, 곱셈-누적) 연산**에 특화되어 있기 때문입니다!

이번 **`mini-npu-simulator`** 프로젝트에서는 NPU의 MAC 연산 엔진을 파이썬으로 직접 모델링하고, 메모리 접근 최적화 성능을 분석해 보았습니다. ⚡

---

### 🔢 MAC(Multiply-Accumulate) 연산이란?

$Y = A 	imes B + C$ 와 같이 곱셈과 가산(누적)을 한 번의 락(Clock) 주기 내에서 연속 처리하는 연산입니다.

딥러닝의 행렬 곱(Matrix Multiplication)과 컨볼루션(Convolution) 연산은 수천만 번의 MAC 연산의 연속으로 이루어집니다.

---

### 🚀 성능 최적화의 비밀: 2D 배열 Flattening (메모리 접근 패턴 개선)

파이썬의 2차원 리스트 `[[...], [...]]` 는 메모리상에 연속적으로 배치되지 않고 포인터를 타고 이동하므로 캐시 미스(Cache Miss)가 자주 발생합니다.

이를 해결하기 위해 2차원 데이터를 1차원 연속 배열로 펼치는 **Flattening** 기법을 적용했습니다.

```python
# 1D Flattening 기반 고속 MAC 연산
def mac_engine_flattened(matrix_a_1d, matrix_b_1d, size):
    accumulator = 0.0
    for i in range(size * size):
        accumulator += matrix_a_1d[i] * matrix_b_1d[i]
    return accumulator
```

메모리 레이아웃을 1차원 연속 배열로 변환하는 것만으로도 데이터 탐색 속도가 대폭 향상되는 결과를 눈으로 직접 확인할 수 있었습니다!

AI 소프트웨어 분야를 넘어서 하드웨어 엔진의 처리 원리까지 엿볼 수 있었던 무척 흥미진진한 연구였습니다! 💡
