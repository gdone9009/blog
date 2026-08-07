---
layout: post
title: "⚡ 파이썬 dict 없이 밑바닥부터 인메모리 Redis 캐시 엔진 만들기 (Mini-Redis 구현기)"
date: 2026-08-02 10:00:00 +0900
tags: [Python, DataStructures, Cache, MiniRedis, Codyssey]
category: Codyssey-Mission
---

개발자라면 누구나 한 번쯤 들어보았을 **Redis(레디스)**!  
속도가 엄청나게 빠른 인메모리 Key-Value 데이터 저장소죠. 

파이썬에서 기본 제공하는 `dict`나 `set` 컬렉션을 일절 쓰지 않고, 이중 연결 리스트, 체이닝 해시맵, 최소 힙으로 바닥부터 직접 캐시 엔진을 설계했습니다! 🛠️

---

### 💡 바이트 단위 메모리 계측 & LRU Eviction

데이터가 추가될 때마다 문자열 바이트 크기를 계산하여 `used_memory`를 실시간 계측하고, 메모리 상한을 초과하는 순간 이중 연결 리스트의 Tail 노드를 제거하는 LRU 파이프라인을 구축했습니다.

```python
def _check_eviction(self):
    while self.used_memory > self.max_memory:
        lru_node = self.lru_list.remove_tail()
        if lru_node:
            self.hash_map.delete(lru_node.key)
            self.used_memory -= lru_node.byte_size
            print(f"[Eviction] LRU 키 삭제 완료: {lru_node.key}")
```

자료구조 노드 포인터 조작의 진수를 맛볼 수 있었던 멋진 프로젝트였습니다! 🔥
