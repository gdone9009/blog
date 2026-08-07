---
layout: post
title: "⚡ 파이썬 dict 없이 밑바닥부터 인메모리 Redis 캐시 엔진 만들기 (Mini-Redis 구현기)"
slug: mini-redis
date: 2026-08-02 10:00:00 +0900
tags: [Python, DataStructures, Cache, MiniRedis, Codyssey]
category: Codyssey-Mission
---

# ⚡ mini-redis 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

Redis(REmote DIctionary Server)는 전 세계 대규모 서비스에서 초당 수십만 건의 Read/Write 요청을 처리하는 초고속 인메모리 데이터 저장소입니다.

**`mini-redis`** 미션의 핵심 질문은 이것이었습니다:  
**"파이썬 내장 `dict`나 `set` 같은 편의용 추상화 컬렉션을 일절 사용하지 않고, 4대 로우레벨 자료구조(이중 연결 리스트, 체이닝 해시맵, 최소 힙, BST)만으로 Redis의 캐시 엔진과 LRU Eviction, TTL 만료 알고리즘을 구현할 수 있는가?"**

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: C 스타일 로우레벨 자료구조 컴포지션

상위 추상화된 `dict` 모듈은 내부 메모리 배치와 해시 충돌(Hash Collision)을 알아서 처리합니다. 하지만 밑바닥 레이어를 지배하기 위해 노드 기반 알고리즘을 직접 작성했습니다.

```text
[ Hash Map (Chaining) ] ─── Bucket Index ───> [ Linked List Node (Key, Value) ]
                                                        │ (Doubly Linked)
[ LRU Eviction List ]   ─── Head (Recently Used) <──────┴──────> Tail (Oldest - Evict Target)
```

1. **이중 연결 리스트 (Doubly Linked List)**: 노드의 `prev`, `next` 포인터를 제어하여 $O(1)$ 속도로 LRU Head/Tail 이동 및 삭제
2. **체이닝 해시맵 (Chaining Hash Map)**: 모듈로(`%`) 해시 함수로 버킷 인덱스를 구하고, 해시 충돌 시 연결 리스트로 노드를 연속 연결
3. **최소 힙 (Min Heap)**: 키 만료 시각(TTL Expiration Time)을 정렬 상태로 유지하여 $O(1)$ 만에 만료 키 추출

---

### 쟁점 2: 바이트 단위 메모리 계측 & LRU(Least Recently Used) Eviction

인메모리 캐시는 물리 메모리(RAM) 용량이 제한되어 있습니다. 데이터가 무한히 쌓이면 서버가 OOM(Out of Memory)으로 다운됩니다.

#### 💡 실시간 `sys.getsizeof` 계측 및 LRU 알고리즘 구현

```python
import sys

class MiniRedisEngine:
    def __init__(self, max_memory_bytes=1024 * 1024): # 1MB 상한
        self.max_memory = max_memory_bytes
        self.used_memory = 0
        self.hash_map = ChainingHashMap()
        self.lru_list = DoublyLinkedList()

    def set(self, key: str, value: str):
        # 1. 키-값의 바이트 오버헤드 정밀 계산
        entry_bytes = sys.getsizeof(key) + sys.getsizeof(value)

        # 2. 메모리 상한 초과 시 LRU Eviction (Tail 노드 삭제)
        while self.used_memory + entry_bytes > self.max_memory:
            oldest_node = self.lru_list.pop_tail() # $O(1)$ 제거
            if not oldest_node:
                break
            self.hash_map.remove(oldest_node.key)
            self.used_memory -= oldest_node.byte_size
            print(f"🚨 [LRU Eviction] 메모리 확보를 위해 오래된 키 삭제: {oldest_node.key}")

        # 3. 신규 노드를 Head에 삽입
        new_node = Node(key, value, entry_bytes)
        self.hash_map.put(key, new_node)
        self.lru_list.push_head(new_node)
        self.used_memory += entry_bytes
```

---

### 쟁점 3: Min-Heap 기반 TTL(Time-To-Live) 정렬 만료

10만 개의 키가 등록되어 있을 때 매 초마다 전체 키를 풀 스캔(Full Scan)하여 만료 여부를 체크하는 것은 $O(N)$의 거대한 CPU 낭비입니다.

#### 💡 최소 힙(Min-Heap)으로 $O(1)$ 만료 키 감지

만료 시간이 가장 적게 남은(가장 빨리 만료될) 키를 최소 힙의 Root에 위치시킵니다:
```python
def check_ttl_expiration(self, current_timestamp: float):
    # Root 노드의 만료 시각만 검사 (Time Complexity: $O(1)$)
    while self.min_heap.peek() and self.min_heap.peek().expire_at <= current_timestamp:
        expired_item = self.min_heap.pop_root() # $O(\log N)$ 재정렬
        self.delete(expired_item.key)
        print(f"⏰ [TTL Expired] 만료된 키 자동 제거: {expired_item.key}")
```

---

## 📝 4. 결론 및 성과

- **자료구조 직접 구현**: 파이썬 `dict`를 쓰지 않고 노드 포인터 기반 인메모리 저장소 구축
- **메모리 안정성**: 바이트 단위 계측 및 LRU Eviction으로 OOM 방지
- **고성능 알고리즘**: Min-Heap TTL 정렬로 만료 키 검사 $O(1)$ 최적화 🚀
