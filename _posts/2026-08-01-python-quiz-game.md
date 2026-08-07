---
layout: post
title: "🎯 파이썬 OOP 첫 걸음! JSON 연동 CLI 퀴즈 게임 개발기 (python-quiz-game)"
date: 2026-08-01 14:00:00 +0900
tags: [Python, OOP, JSON, QuizGame, Codyssey]
category: Codyssey-Mission
---

파이썬의 단순한 함수와 `if` 문 위주의 절차지향 코드 작성에서 벗어나, **객체지향 프로그래밍(OOP)** 패턴을 습득하고 데이터를 영속화하는 파이프라인을 구축해 보는 미션이었습니다! 🎮

---

### 🧩 핵심 엔지니어링 포인트

1. **퀴즈 엔진 객체화 (`Quiz`, `QuestionManager`)**:
   - 질문 지문, 4지 선다 보기, 정답 번호, 부연 설명을 다루는 `Question` 클래스 정의
   - 사용자 입력을 검증하고 점수를 누적하는 `QuizEngine` 분리

2. **JSON 기반 상태 영속화 (`state.json`)**:
   - 게임 실행 종료 후에도 사용자의 이전 점수와 진행 상태가 저장되도록 파이썬 `json` 모듈 활용

```python
import json

class QuizState:
    def __init__(self, filepath="state.json"):
        self.filepath = filepath

    def save_score(self, score, total):
        data = {"last_score": score, "total_questions": total}
        with open(self.filepath, "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
```

직관적인 1~4 번호 입력 UX와 정답 맞춤형 설명이 어우러진 CLI 게임을 만들어 보며 파이썬 OOP의 뼈대를 단단히 세울 수 있었습니다! 👍
