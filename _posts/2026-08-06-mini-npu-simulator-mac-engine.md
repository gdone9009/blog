---
layout: post
title: "🧠 NPU는 왜 AI 연산에 빠를까? 파이썬으로 직접 만드는 NPU MAC 연산 시뮬레이터"
date: 2026-08-06 13:10:00 +0900
tags: [AI, NPU, Python, MatrixMultiplication, Hardware, Codyssey]
category: Codyssey-Mission
---

AI 가속기(NPU)의 핵심 연산 단위인 MAC(Multiply-Accumulate) 연산 알고리즘을 파이썬으로 모델링하고 성능을 분석한 미션입니다! ⚡

2차원 배열 데이터를 1차원으로 펼치는 **Flattening** 기법을 적용하여 캐시 미스(Cache Miss)를 줄이고 메모리 접근 성능을 획기적으로 개선했습니다.
