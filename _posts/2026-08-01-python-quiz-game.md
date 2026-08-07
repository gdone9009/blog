---
layout: post
title: "🎯 파이썬 OOP 첫 걸음! JSON 연동 CLI 퀴즈 게임 개발기 (python-quiz-game)"
slug: python-quiz-game
date: 2026-08-01 14:00:00 +0900
tags: [Python, OOP, JSON, QuizGame, Codyssey]
category: Codyssey-Mission
---

# 🎯 python-quiz-game 미션 기술 딥다이브

안녕하세요! 오늘 소개해 드릴 프로젝트는 파이썬 첫 걸음 미션인 **`python-quiz-game`** 입니다.

단순히 퀴즈 몇 개 풀고 끝나는 콘솔 프로그램처럼 보이지만, README를 열어보면 개발자로서 고민해야 했던 **두 가지 핵심 기술 쟁점**이 숨어 있습니다. 함께 살펴볼까요? 😊

---

## ⚡ 쟁점 1: 절차지향 `if-else` 작성 vs OOP 클래스 데이터 캡슐화

초보 개발 시절에는 퀴즈 게임을 만들 때 보통 아래처럼 절차지향적으로 작성하곤 합니다:

```python
# ❌ 절차지향적 방식: 데이터와 로직이 뒤섞여 유지보수가 어려움
q1 = "영화 아바타의 감독은?"
ans1 = "제임스 카메론"
user_input = input(q1)
if user_input == ans1:
    score += 1
```

하지만 이 방식은 문제가 10개, 100개로 늘어나거나 4지 선다형 선택지, 정답 해설, 난이도가 추가되면 코드가 엉망이 됩니다.

### 💡 공학적 해결책: `Question` & `QuizEngine` OOP 객체 설계

`python-quiz-game`에서는 질문 지문, 선택지 리스트, 정답 인덱스, 해설을 하나의 단위로 캡슐화한 `Question` 클래스를 도입했습니다:

```python
class Question:
    def __init__(self, prompt, options, answer_index, explanation):
        self.prompt = prompt
        self.options = options
        self.answer_index = answer_index
        self.explanation = explanation

    def is_correct(self, user_choice):
        return user_choice == self.answer_index
```

이렇게 데이터를 객체로 만드니 `QuizEngine`은 문제가 무엇인지 몰라도 순회(Loop)만 돌면서 평가할 수 있게 되어 **코드의 재사용성과 확장성이 비약적으로 상승**했습니다!

---

## ⚡ 쟁점 2: 프로그램 종료 시 점수가 사라지는 문제 ➡️ JSON 데이터 영속화

콘솔 프로그램이 종료되면 메모리(RAM)에 있던 변수들은 모두 사라집니다. 다음번에 게임을 켰을 때 "내가 지난번에 몇 점 받았지?"를 알 수 없게 되죠.

### 💡 공학적 해결책: `state.json` 구조화 저장

데이터베이스(DB)를 거창하게 설치하지 않고도, 경량 구조 데이터 포맷인 **JSON**을 사용하여 이전 실행 기록을 로컬 디스크에 영속화(Persistence)했습니다.

```json
{
  "last_played_at": "2026-08-01 14:00:00",
  "high_score": 5,
  "total_questions": 5,
  "history": [
    { "round": 1, "score": 4 },
    { "round": 2, "score": 5 }
  ]
}
```

- 파이썬의 `json.dump()`와 `json.load()`를 활용하여 프로그램 시작 시 `state.json`을 읽어 최고 점수를 표시하고, 게임 종료 시 직렬화하여 안전하게 기록하도록 구현했습니다.

---

## 📝 요약 및 성과

`python-quiz-game` 미션을 통해 절차지향에서 **객체지향(OOP) 데이터 캡슐화**로 사고의 전환을 이뤄냈으며, 파이썬 내장 `json` 모듈로 파일 기반 데이터 영속화의 기초를 단단히 세웠습니다! 🚀
