---
layout: post
title: "🎨 React 없이 바닐라 자바스크립트로 단방향 상태 관리 아키텍처 구축하기"
date: 2026-08-07 15:45:00 +0900
tags: [JavaScript, Frontend, Architecture, StateManagement, Codyssey]
category: Codyssey Mission
---

React, Vue 같은 프론트엔드 프레임워크를 사용하면 상태(State)가 바뀔 때 알아서 화면이 척척 갱신됩니다.  
하지만 외부 라이브러리의 도움 없이 **순수 바닐라 자바스크립트(Vanilla JS)**만으로 단방향 상태 관리 엔진을 직접 만든다면 어떻게 해야 할까요?

이번 **`vanilla-js-portfolio`** 미션은 **"사용자 이벤트 ➡️ 상태 업데이트 ➡️ DOM 렌더링"**으로 이어지는 선언형 단방향 데이터 흐름 아키텍처를 바닥부터 직접 구현한 포트폴리오 웹 개발기입니다! 💻

---

### 🔄 단방향 데이터 흐름 메커니즘 (Event -> State -> Render)

```text
[User Interaction]
       │
       ▼
(Trigger Event Listener)
       │
       ▼
[Update State Store]
       │
       ▼
(Invoke Render Function)
       │
       ▼
[DOM Mutate & UI Update]
```

1. 사용자가 다크 모드 버튼을 누르거나 필터링 키워드를 입력합니다.
2. 이벤트 핸들러는 DOM을 직접 수정하는 대신 **전역 `state` 객체**만 변경합니다.
3. `render()` 함수가 호출되어 최신 `state` 상태를 바탕으로 화면을 효율적으로 다시 그립니다.

---

### 🛡️ GitHub API 4단계 UI 상태 머신 (State Machine)

비동기 API 통신 시 사용자가 지루하지 않도록 4가지 UI 상태를 명확히 분리하여 대응했습니다:

- ⏳ **`loading`**: 애니메이션 스피너 표시
- ✅ **`success`**: 오픈소스 프로젝트 카드 생성 및 렌더링
- ❌ **`error / rate-limit`**: API 403 대응 및 다시 시도 버튼 제공
- 📭 **`empty`**: 검색 결과가 없을 때 안내 메시지 노출

프레임워크의 추상화된 편리함 뒤에 숨겨진 브라우저 DOM 제어와 단방향 데이터 아키텍처의 원리를 완벽하게 체득한 무척 보람찬 프로젝트였습니다! 🔥
