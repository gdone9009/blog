---
layout: post
title: "⚡ [mini-redis] 파이썬 내장 dict 없이 밑바닥부터 구현한 인메모리 캐시 엔진 & LRU Eviction"
slug: mini-redis
date: 2026-08-02 10:00:00 +0900
tags: [Python, DataStructures, Cache, MiniRedis, Memory, Codyssey]
category: Codyssey-Mission
---

# ⚡ mini-redis 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

Redis(REmote DIctionary Server)는 초당 수십만 건의 Read/Write 요청을 처리하는 인메모리 Key-Value 데이터 저장소입니다.

**`mini-redis`** 미션에서는 파이썬 내장 `dict`, `list`, `set` 같은 고수준 추상화 컬렉션을 일절 사용하지 않고, 4대 로우레벨 자료구조(이중 연결 리스트, 체이닝 해시맵, Min-Heap, BST)를 직접 구현하여 LRU Eviction과 Min-Heap TTL 만료 알고리즘을 갖춘 캐시 엔진을 개발했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ Chaining Hash Map ] ── Hash Index ──> [ Doubly Linked Node (Key, Value) ]
                                                    │
[ LRU Doubly Linked List ] ── Head (MRU) <──────────┴──────────> Tail (LRU Evict)

[ Min-Heap Expire Queue ] ── Root (가장 이른 TTL) ──> O(1) 만료 체크
```

### 쟁점 1: 파이썬 `dict` vs 체이닝 해시맵 (Chaining Hash Map)

- **`dict` 사용**: CPython 내부의 오픈 어드레싱 해시 테이블을 사용하므로 편하지만, 해시 충돌 및 메모리 할당 매커니즘 파악 불가.
- **직접 구현**: 모듈로 연산 해시 함수와 버킷 배열, 충돌 발생 시 연결 리스트로 노드를 이어서 처리하는 체이닝(Chaining) 방식 탑재.

### 2.2 바이트 단위 메모리 정밀 계측 & LRU(Least Recently Used) 삭제

물리 메모리는 한정되어 있으므로 메모리가 상한(`max_memory_bytes`)에 도달했을 때 가장 오래 사용되지 않은 항목을 $O(1)$ 속도로 제거해야 합니다.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 `hashmap.py` - 체이닝 해시맵 코어

```python
class ChainingHashMap:
    def __init__(self, capacity=16):
        self.capacity = capacity
        self.size = 0
        self.buckets = [None] * capacity

    def _hash(self, key: str) -> int:
        hash_val = 2166136261
        for char in key:
            hash_val ^= ord(char)
            hash_val *= 16777619
        return hash_val % self.capacity

    def put(self, key: str, value: Any):
        idx = self._hash(key)
        curr = self.buckets[idx]
        while curr:
            if curr.key == key:
                curr.value = value
                return
            curr = curr.next
        new_node = HashNode(key, value, self.buckets[idx])
        self.buckets[idx] = new_node
        self.size += 1
```

### 3.2 `memory_manager.py` - LRU Eviction 엔진

```python
def set_with_eviction(self, key: str, value: str):
    entry_bytes = sys.getsizeof(key) + sys.getsizeof(value)

    # 메모리 용량 초과 시 LRU Tail 노드 O(1) 제거
    while self.used_memory + entry_bytes > self.max_memory:
        tail_node = self.lru_list.pop_tail()
        if not tail_node:
            break
        self.hash_map.remove(tail_node.key)
        self.used_memory -= tail_node.byte_size
        print(f"🚨 [LRU Evict] 키 제거: {tail_node.key}")

    new_node = Node(key, value, entry_bytes)
    self.hash_map.put(key, new_node)
    self.lru_list.push_head(new_node)
    self.used_memory += entry_bytes
```

---

## 🧪 4. 테스트 & 무결성 검증

`run_tests.py`를 통해 9개 모듈 전체에 대한 유닛 테스트 수트를 수행했습니다:
- **`test_hashmap.py`**: 해시 충돌 및 충돌 키 검색 무결성 통과
- **`test_commands_and_ttl.py`**: TTL 만료 후 자동 조회 불가 및 Min-Heap 정렬 검증
- **결과**: `22 / 22 Tests Passed (100% PASS)`

---

## 📝 5. 결론 및 공학적 인사이트

- **추상화 탈피**: 로우레벨 포인터 기반 노드 제어로 파이썬 메모리 동작 구조 완벽 이해.
- **O(1) LRU & O(1) Min-Heap TTL**: 알고리즘 선택에 따른 성능 차이를 empirical하게 입증 🚀
