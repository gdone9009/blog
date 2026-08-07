---
layout: post
title: "[2026-08-07] vanilla-js-portfolio"
date: 2026-08-07 15:45:00 +0900
tags: [JavaScript, Frontend, Architecture, StateManagement, Codyssey]
category: Codyssey-Mission
---

# 🎨 [2026-08-07] vanilla-js-portfolio 미션 기술 딥다이브

React, Vue 같은 현대 프론트엔드 프레임워크는 상태(State)가 바뀔 때 DOM을 알아서 업데이트해 줍니다.

이번 **`vanilla-js-portfolio`** 미션의 핵심 쟁점은 **"외부 라이브러리의 추상화 레이어에 의존하지 않고, 순수 바닐라 자바스크립트(Vanilla JS ES6+)만으로 단방향 상태 관리 아키텍처를 어떻게 직접 설계할 것인가?"** 였습니다. 💻

---

## ⚡ 쟁점 1: 직접적인 DOM 조작(Spaghetti Code) vs 선언형 단방향 상태 흐름

`document.getElementById().innerText = ...` 처럼 렌더링 코드와 이벤트 코드가 엉키면 스파게티 코드가 됩니다.

### 💡 공학적 해결책: Event ➡️ State Store ➡️ Render Engine 단방향 구조

```text
[User Action] ---> (Trigger Event) ---> [Update State Store] ---> (Invoke Render) ---> [DOM Update]
```

```javascript
// 전역 단방향 상태 스토어 (State Store)
const state = {
  theme: 'dark',
  projects: [],
  currentFilter: 'all',
  projectsStatus: 'loading' // 'loading' | 'success' | 'error' | 'empty'
};

// 상태 변경 시 렌더링 트리거
function setState(newState) {
  Object.assign(state, newState);
  render(); // DOM 갱신 단일 창구
}
```

---

## ⚡ 쟁점 2: 비동기 REST API 레이트 리밋 방어 & 4단계 UI 상태 머신 (State Machine)

GitHub REST API (`https://api.github.com/users/gdone9009/repos`)를 호출할 때 API 403 Rate Limit 장애가 발생하는 쟁점이 있었습니다.

### 💡 공학적 해결책: 15분 Cache & 4단계 UI 상태 머신

1. `sessionStorage` 기반 15분 임시 데이터 캐싱으로 불필요한 API 재요청 차단
2. **4단계 UI 상태 머신**:
   - `loading`: 동적 CSS 스피너 표시
   - `success`: 오픈소스 프로젝트 카드 렌더링
   - `error / rate-limit`: 에러 안내 UI + [다시 시도 / 폴백 데이터 로드] 버튼
   - `empty`: 검색 결과 없음 안내 UI

---

## 📝 요약 및 성과

프레임워크 없이 순수 웹 표준 기술만으로 단방향 상태 관리 아키텍처, CSS3 디자인 토큰 반응형 레이아웃, API 레이트 리밋 방어 체계를 완성했습니다! 🚀
