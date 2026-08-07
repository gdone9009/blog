---
layout: post
title: "⚡ 파이썬 dict 없이 밑바닥부터 인메모리 Redis 캐시 엔진 만들기 (Mini-Redis 구현기)"
slug: mini-redis
date: 2026-08-02 10:00:00 +0900
tags: [Python, DataStructures, Cache, MiniRedis, Codyssey]
category: Codyssey-Mission
---

# ⚡ mini-redis 미션 기술 딥다이브

오늘 다룰 프로젝트는 이번 커리큘럼의 꽃이라 불리는 **`mini-redis`** 입니다!  
Redis(레디스)는 전 세계 수많은 대규모 서비스에서 사용하는 초고속 인메모리 Key-Value 데이터 저장소죠.

이 미션의 핵심 쟁점은 **"파이썬 내장 `dict`나 `set` 같은 편리한 컬렉션을 일절 쓰지 않고, 순수 로우레벨 자료구조만으로 Redis 핵심 인메모리 캐시 엔진을 바닥부터 구현할 수 있는가?"** 였습니다. 🛠️

---

## ⚡ 쟁점 1: 파이썬 `dict`를 쓰지 않는 C 스타일 자료구조 컴포지션

상위 추상화 라이브러리인 `dict`는 내부적으로 알아서 해시 충돌을 해결해 줍니다. 하지만 하부 레이어를 지배하려면 포인터와 노드를 직접 제어해야 합니다.

### 💡 공학적 해결책: 4대 로우레벨 자료구조 직접 구현

1. **이중 연결 리스트 (Doubly Linked List)**: LRU(Least Recently Used) 알고리즘을 위해 노드의 양방향 포인터(`prev`, `next`)를 제어하여 $O(1)$ 속도로 노드 이동/삭제
2. **체이닝 해시맵 (Chaining Hash Map)**: 해시 함수로 인덱스를 계산하고, 해시 충돌 발생 시 연결 리스트 노드로 체이닝(Chaining)하여 Key-Value 저장
3. **최소 힙 (Min Heap)**: 키마다 설정된 만료 시간(TTL) 중 가장 먼저 만료될 키를 $O(1)$ 만에 즉시 팝(Pop)하기 위해 구현
4. **이진 탐색 트리 (BST)**: 정렬 탐색 및 범위 검색 지원

---

## ⚡ 쟁점 2: 실시간 메모리 오버헤드 계측 vs LRU Eviction 파이프라인

인메모리 저장소는 물리 메모리(RAM) 용량이 한정되어 있으므로, 무제한으로 데이터를 넣으면 서버가 OOM(Out of Memory)으로 사망합니다.

### 💡 공학적 해결책: 바이트 단위 `used_memory` 계측 & LRU 노드 제거

데이터가 들어올 때마다 문자열 바이트 크기를 정밀 계산하여 메모리 사용량을 동적으로 추적합니다.  
메모리 상한 임계치(예: 1MB)를 초과하면 이중 연결 리스트의 Tail(가장 오랫동안 사용되지 않은 노드)을 즉시 삭제합니다!

```python
def put(self, key, value):
    byte_size = sys.getsizeof(key) + sys.getsizeof(value)
    
    # 1. 메모리 임계치 초과 시 LRU Eviction 트리거
    while self.used_memory + byte_size > self.max_memory:
        lru_node = self.lru_list.remove_tail() # 가장 오래된 노드 $O(1)$ 삭제
        if lru_node:
            self.hash_map.delete(lru_node.key)
            self.used_memory -= lru_node.byte_size
            print(f"[LRU Evict] 메모리 확보를 위해 키 삭제: {lru_node.key}")
            
    # 2. 신규 노드를 Head에 삽입
    new_node = Node(key, value, byte_size)
    self.hash_map.insert(key, new_node)
    self.lru_list.add_head(new_node)
    self.used_memory += byte_size
```

---

## ⚡ 쟁점 3: Min-Heap 기반 TTL(Time To Live) 만료 키 정렬 삭제

키에 10초, 30초 뒤 만료(Expiration) 조건을 걸었을 때, 100만 개의 키를 매초 풀스캔(Full Scan)하여 만료 여부를 검사하는 것은 엄청난 CPU 낭비입니다.

### 💡 공학적 해결책: 최소 힙(Min Heap)으로 만료 시각 관리

만료 시각이 가장 작은(가장 빨리 만료되는) 키가 Min Heap의 Root에 오도록 설계하여, **Root의 만료 시각만 현재 시각과 비교**함으로써 $O(1)$ 만에 만료된 키를 즉시 감지하고 정렬 팝(Pop) 처리했습니다.

---

## 📝 요약 및 성과

`mini-redis` 미션을 통해 Redis 하부 레이어의 메모리 계측, LRU 캐시 교체 알고리즘, TTL 만료 처리 메커니즘을 자료구조적 관점에서 역추적 체득하는 엄청난 공학적 성과를 이루었습니다! 🚀
