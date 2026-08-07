---
layout: post
title: "⚡ 파이썬 dict 없이 밑바닥부터 인메모리 Redis 캐시 엔진 만들기 (Mini-Redis 구현기)"
date: 2026-08-02 10:00:00 +0900
tags: [Python, DataStructures, Cache, MiniRedis, Codyssey]
category: Codyssey-Mission
---

개발자라면 누구나 한 번쯤 들어보았을 **Redis(레디스)**!  
속도가 엄청나게 빠른 인메모리 Key-Value 데이터 저장소죠. 그런데 문득 이런 생각이 들었습니다.

> *"파이썬에서 기본 제공하는 `dict`나 `set` 같은 편리한 컬렉션을 하나도 쓰지 않고, 순수 자료구조만으로 Redis의 LRU Eviction과 TTL 만료 삭제를 직접 구현할 수 있을까?"*

이번 **`mini-redis`** 과제가 바로 이 거대한 호기심을 해소하는 미션이었습니다! 🛠️

---

### 🧩 사용한 로우레벨 자료구조 4대장

우선 파이썬의 `dict`에 의존하지 않기 위해 다음 자료구조들을 바닥부터 직접 파이썬 클래스로 구현했습니다.

1. **이중 연결 리스트 (Doubly Linked List)**: LRU(Least Recently Used) 캐시 알고리즘에서 노드를 $O(1)$로 빠르게 맨 앞으로 옮기거나 삭제할 때 사용
2. **체이닝 해시맵 (Chaining Hash Map)**: 해시 충돌(Collision)을 연결 리스트로 해결하여 Key-Value 탐색을 고속 처리
3. **최소 힙 (Min Heap)**: 키(Key)마다 설정된 만료 시간(TTL) 중 **가장 먼저 만료될 키**를 $O(1)$만에 즉시 팝(Pop)하기 위해 사용
4. **이진 탐색 트리 (BST)**: 범위 검색 및 키 정렬을 위해 구축

---

### 💡 가장 흥미로웠던 도전: 바이트 단위 물리 메모리 실시간 계측 & LRU Eviction

Redis가 고성능인 이유는 메모리가 꽉 차기 전에 **가장 오래 사용되지 않은 키를 자동으로 지워주는 LRU(Least Recently Used)** 정책이 작동하기 때문입니다.

`mini-redis`에서는 데이터가 추가될 때마다 문자열 바이트 크기를 계산하여 `used_memory`를 실시간 계측했습니다.  
그리고 메모리 상한 임계치(예: 1MB)를 초과하는 순간! 이중 연결 리스트의 Tail에 위치한 **가장 오래된 데이터를 즉시 Evict(격리/삭제)** 하도록 만들었습니다.

```python
# LRU Eviction 핵심 로직 요약
def _check_eviction(self):
    while self.used_memory > self.max_memory:
        # 가장 오래 사용되지 않은 tail 노드 추출 및 삭제
        lru_node = self.lru_list.remove_tail()
        if lru_node:
            self.hash_map.delete(lru_node.key)
            self.used_memory -= lru_node.byte_size
            print(f"[Eviction] LRU 키 삭제 완료: {lru_node.key}")
```

---

### 🎉 완성 후 느낀 점

외부 라이브러리가 대신 처리해 주던 메모리 계측과 노드 이동 포인터 연산을 직접 구현해 보면서, Redis 같은 고성능 캐시 시스템이 얼마나 정교하게 디자인되었는지 깊게 이해할 수 있었습니다. 

프레임워크의 편리함에 가려졌던 컴퓨터 공학 기초 자료구조의 위대함을 다시 한번 깨닫는 값진 시간이었습니다! 🔥
