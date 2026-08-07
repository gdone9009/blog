---
layout: post
title: "🎨 React 없이 바닐라 자바스크립트로 단방향 상태 관리 아키텍처 구축하기"
slug: vanilla-js-portfolio
date: 2026-08-07 15:45:00 +0900
tags: [JavaScript, Frontend, Architecture, StateManagement, Codyssey]
category: Codyssey-Mission
---

# 🎨 vanilla-js-portfolio 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

React나 Vue 같은 현대 프론트엔드 프레임워크는 상태(State) 변경에 따른 DOM 업데이트를 자동으로 가상 DOM(Virtual DOM)이 처리해 줍니다.

**`vanilla-js-portfolio`** 미션에서는 라이브러리 없이 순수 바닐라 자바스크립트(ES6+)만으로 **단방향 상태 관리 아키텍처(Event ➡️ State Store ➡️ Render Engine)** 를 바닥부터 설계했습니다.

---

## ⚡ 2. 핵심 기술 쟁점 (Technical Debates & Trade-offs)

### 쟁점 1: 스파게티 DOM 조작 vs 단방향 상태 관리 아키텍처

```javascript
// 전역 단방향 상태 스토어
const state = {
  theme: 'dark',
  projects: [],
  status: 'loading' // 'loading' | 'success' | 'error' | 'empty'
};

function setState(newState) {
  Object.assign(state, newState);
  render(); // 단일 DOM 렌더링 창구 호출
}
```

### 쟁점 2: GitHub API 403 Rate Limit 방어 & 15분 SessionStorage 캐싱

```javascript
async function fetchProjects() {
  const cached = sessionStorage.getItem('github_repos');
  if (cached) return JSON.parse(cached);

  const res = await fetch('https://api.github.com/users/gdone9009/repos');
  const data = await res.json();
  sessionStorage.setItem('github_repos', JSON.stringify(data));
  return data;
}
```

---

## 📝 4. 결론 및 성과

- **단방향 아키텍처**: 외부 라이브러리 없이 순수 JS 상태 스토어 구축
- **API 레이트 리밋 방어**: SessionStorage 캐싱 및 4단계 UI 상태 머신 완성 🚀
