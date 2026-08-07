---
layout: post
title: "🎯 파이썬 OOP 첫 걸음! JSON 연동 CLI 퀴즈 게임 개발기 (python-quiz-game)"
slug: python-quiz-game
date: 2026-08-01 14:00:00 +0900
tags: [Python, OOP, JSON, QuizGame, Codyssey]
category: Codyssey-Mission
---

# 🎯 python-quiz-game 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

파이썬 프로그래밍에 처음 입문할 때 가장 흔히 만드는 프로젝트가 콘솔 기반 퀴즈 게임입니다. 하지만 단순히 `input()`으로 입력을 받고 `if/else` 문으로 정답을 확인하는 수준에 그친다면, 공학적인 고찰이 부족한 절차지향 스크립트에 불과합니다.

**`python-quiz-game`** 미션에서는 파이썬 기본 문법을 넘어서 **객체지향 프로그래밍(OOP)을 통한 역할 분리(SoC)** 와 **JSON 데이터 영속화(Data Persistence)** 를 직접 설계하는 것을 목표로 삼았습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: 절차지향 스파게티 코드 vs OOP 캡슐화(Encapsulation)

#### ❌ 기존 절차지향 방식의 한계
초기에는 퀴즈 문제를 파이썬 튜플이나 다중 리스트로 관리하곤 합니다:

```python
# ❌ 기존 방식: 데이터 구조 변경 시 모든 코드 수정 필요
questions = [
    ("파이썬의 창시자는?", ["가도 ヴァン 로섬", "귀도 반 로섬", "제임스 고슬링"], 1),
    ("C언어를 개발한 인물은?", ["데니스 리치", "켄 톰슨", "비야네 스트롭스트룹"], 0)
]

score = 0
for q, opts, ans in questions:
    print(q)
    # ... 출력 및 검증 로직이 순회문 안에 뒤엉킴
```

이 방식은 선택지 개수가 변경되거나, 정답 해설(Explanation), 퀴즈 카테고리, 난이도 속성이 추가될 때 순회문 내부 전체를 뜯어고쳐야 하는 **강한 결합도(Tight Coupling)** 문제를 야기합니다.

#### 💡 공학적 해결책: `Question` & `QuizEngine` 객체 모델링

`Question` 클래스를 정의하여 데이터와 검증 로직을 하나의 캡슐(Capsule)로 묶었습니다:

```python
class Question:
    def __init__(self, prompt: str, options: list, answer_index: int, explanation: str = ""):
        self.prompt = prompt
        self.options = options
        self.answer_index = answer_index
        self.explanation = explanation

    def is_correct(self, user_choice: int) -> bool:
        """유저의 선택 번호가 정답 인덱스와 일치하는지 내부 검증"""
        return user_choice == self.answer_index

    def display(self):
        """질문 지문 및 선택지 렌더링"""
        print(f"❓ {self.prompt}")
        for idx, opt in enumerate(self.options, 1):
            print(f"  {idx}. {opt}")
```

이렇게 데이터를 객체화함으로써 `QuizEngine`은 질문 내부가 어떻게 생겼는지 몰라도 퀴즈를 진행할 수 있게 되었습니다!

---

### 쟁점 2: 휘발성 메모리 데이터 ➡️ `state.json` 구조화 영속화

프로그램이 종료되면 RAM 메모리에 있던 `score` 변수는 즉시 사라집니다. 다음 실행 시 "나의 통산 최고 점수"나 "퀴즈 이력"을 유지하려면 디스크 저장(Persistence)이 필수적입니다.

#### 💡 `state.json` 스키마 설계 및 파이썬 `json` 모듈 처리

```json
{
  "user_profile": {
    "total_games_played": 12,
    "high_score": 5,
    "last_played_at": "2026-08-01 14:00:00"
  },
  "history": [
    { "round": 1, "score": 4, "total": 5, "accuracy": 80.0 },
    { "round": 2, "score": 5, "total": 5, "accuracy": 100.0 }
  ]
}
```

```python
import json
import os

class DataManager:
    def __init__(self, filepath="state.json"):
        self.filepath = filepath

    def load_state(self) -> dict:
        """JSON 데이터 안전 로드 (파일 부재 시 기본값 생성)"""
        if not os.path.exists(self.filepath):
            return {"user_profile": {"total_games_played": 0, "high_score": 0}, "history": []}
        try:
            with open(self.filepath, "r", encoding="utf-8") as f:
                return json.load(f)
        except json.JSONDecodeError:
            print("⚠️ state.json 손상 감지. 초기 상태로 복구합니다.")
            return {"user_profile": {"total_games_played": 0, "high_score": 0}, "history": []}

    def save_state(self, state_data: dict):
        """직렬화하여 디스크에 저장"""
        with open(self.filepath, "w", encoding="utf-8") as f:
            json.dump(state_data, f, ensure_ascii=False, indent=2)
```

---

## 🛠️ 3. 실행 파이프라인 및 테스트 방법

```bash
# 1. 저장소 실행
python3 main.py

# 2. Pytest 기반 테스트 수트 검증
pytest tests/
```

---

## 📝 4. 결론 및 성과

- **OOP 데이터 캡슐화**: 데이터 구조 변경 시에도 `QuizEngine` 코드를 건드리지 않는 유지보수성 확보
- **JSON 영속화 패턴**: 파이썬 내장 `json` 모듈로 파일 기반 데이터 영속화 및 예외 복구 로직 완비 🚀
