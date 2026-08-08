---
layout: post
title: "🎯 [python-quiz-game] 객체지향 아키텍처와 JSON 영속성을 결합한 CLI 퀴즈 엔진 구축기"
slug: python-quiz-game
date: 2026-08-01 10:00:00 +0900
tags: [Python, OOP, CLI, JSON, Architecture, Codyssey]
category: Codyssey-Mission
---

# 🎯 python-quiz-game 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

단순한 콘솔 인터렉티브 프로그램은 절차지향적 코드로 빠르게 구현할 수 있지만, 요구사항이 확장됨에 따라 상태 관리 불일치, 입출력 에러 시 프로세스 비정상 종료, 데이터 파일 파손 문제에 직면하게 됩니다.

**`python-quiz-game`** 미션은 다음 핵심 엔지니어링 문제를 해결합니다:
1. **단일 책임 원칙(SRP)** 준수를 위한 데이터 모델(`Quiz`), 컨트롤러(`QuizGame`), 진입점(`main.py`) 3계층 분리
2. 사용자 잘못된 번호 입력시 프로그램이 강제 종료되지 않는 **무한 재입력 수용 루프** 구현
3. JSON 데이터 파일(`state.json`)의 필수 키 누락 및 파손 시 시스템이 멈추지 않고 **자동 기본 데이터로 롤백 및 복구**되는 자가 치유(Self-healing) 매커니즘 구현

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ Main Entrypoint (main.py) ]
          │
          ▼
[ QuizGame Controller (quizgame.py) ] <── JSON Storage (state.json)
          │
          ├──> [ Quiz Model 1 (quiz.py) ]
          ├──> [ Quiz Model 2 (quiz.py) ]
          └──> [ Quiz Model N (quiz.py) ]
```

### 쟁점 1: Dictionary 기반 가변 객체 vs `Quiz` 클래스 캡슐화

- **Dictionary 직접 사용**: 속도는 빠르나 key 오타 발생 시 `KeyError` 발생 및 데이터 타입 검증 불가.
- **Quiz 클래스 도입**: `__init__`을 통해 속성 규격화, `is_correct(choice)`와 같은 비즈니스 로직을 모델 내부로 캡슐화하여 높은 재사용성 확보.

### 쟁점 2: 파일 읽기 실패 시 예외 전파 vs 덤프 롤백 복구

- **예외 전파(Raise)**: 파일 손상 시 즉시 Crash되어 사용자 경험 악화.
- **안전 복구(Self-healing)**: `json.JSONDecodeError` 및 필수 키(`quizzes`, `history`) 누락 감지 시 `set_default_data()`를 통해 정상 상태 복원 후 저장.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 `quiz.py` - 데이터 모델 캡슐화

```python
class Quiz:
    def __init__(self, question, options, answer, category="General", description=""):
        self.question = question
        self.options = options
        self.answer = int(answer)  # 1-based index 정답
        self.category = category
        self.description = description

    def is_correct(self, user_choice: int) -> bool:
        return self.answer == user_choice
```

### 3.2 `quizgame.py` - 자동 복구 및 무한 예외 입력 수용 루프

```python
def load_data(self):
    if not os.path.exists(self.data_file):
        self.set_default_data()
        return

    try:
        with open(self.data_file, 'r', encoding='utf-8') as f:
            data = json.load(f)
            
            # 필수 데이터 누락 감지 시 자동 복구 절차
            if 'history' not in data or 'quizzes' not in data:
                print("⚠️ 필수 데이터 누락 감지: 기본 상태로 복구합니다.")
                self.set_default_data()
                return

            self.high_score = data.get('high_score', 0)
            self.history = data.get('history', [])
            self.quizzes = [Quiz(**q) for q in data.get('quizzes', [])]
    except Exception as e:
        print(f"❌ 데이터 파손 감지 ({e}): 기본값으로 재설정합니다.")
        self.set_default_data()
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **손상된 JSON 입력 테스트**: `state.json`에 비정상적인 문자를 강제로 삽입 후 실행 시 `❌ 데이터 파손 감지` 로그와 함께 기본 데이터로 안전하게 초기화됨을 확인.
2. **입력 예외 테스트**: 문항 정답 선택 시 문자열(`"abc"`) 또는 범위 밖 숫자(`99`) 입력 시 에러 메시지 출력 후 사용자 재입력을 정상 수용함.

---

## 📝 5. 결론 및 공학적 인사이트

- **SRP 아키텍처**: 데이터 모델과 제어 로직의 분리로 코드 유지보수성이 획기적으로 향상됨.
- **자가 치유 데이터 저장소**: 파일 입출력 시 예외 상황에 대한 방어적 프로그래밍의 중요성 입증 🚀
