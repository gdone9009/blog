---
layout: post
title: "🛡️ [vanilla-js-portfolio] 프레임워크 없는 순수 Vanilla JS 단방향 상태 관리 & SPA 반응형 웹"
slug: vanilla-js-portfolio
date: 2026-08-07 16:00:00 +0900
tags: [JavaScript, StateManagement, SPA, GitHubAPI, Pages, Codyssey]
category: Codyssey-Mission
---

# 🛡️ vanilla-js-portfolio 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

React, Vue 등 현대 프론트엔드 프레임워크는 상태 변경에 따른 DOM 업데이트를 자동으로 처리해주지만, 내부 동작 원리를 모르면 불필요한 리렌더링 및 상태 불일치 버그를 유발합니다.

**`vanilla-js-portfolio`** 미션은 외부 라이브러리 없이 순수 JavaScript(ES6+)만으로 **단방향 상태 관리 패턴 (Store ➔ View ➔ Dispatcher ➔ Reducer)**을 직접 구현하고, GitHub REST API와 연동되는 반응형 포트폴리오 SPA 웹사이트를 제작한 미션입니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ User Action (UI Click/Input) ] ── Dispatch(Action) ──> [ Reducer ]
                                                             │ (New State)
[ UI Render (DOM Update) ] <──────── Subscribe ───────── [ Store ]
```

### 쟁점 1: 직접 DOM 조작 vs 단방향 상태 관리 패턴 (Store)

- **직접 DOM 조작**: 코드가 길어질수록 DOM 변경점이 파편화되어 상태 추적이 불가능해짐.
- **Store 중앙 상태 관리**: 상태(State) 변경 시 등록된 모든 구독자(Subscriber)에게 통지되어 일관된 리렌더링 보장.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 단방향 중앙 스토어 패턴 구현

```javascript
class Store {
  constructor(reducer, initialState = {}) {
    this.reducer = reducer;
    this.state = initialState;
    this.listeners = [];
  }

  getState() {
    return this.state;
  }

  dispatch(action) {
    this.state = this.reducer(this.state, action);
    this.notify();
  }

  subscribe(listener) {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }

  notify() {
    this.listeners.forEach(listener => listener(this.state));
  }
}
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **GitHub Pages CI/CD 워크플로우**: `.github/workflows/deploy.yml` 및 `.nojekyll` 파일 배포를 통해 404 에러 없이 gdone9009.github.io 사이트에 정상 서비스 확인.
2. **API 캐싱 검증**: localStorage 기반 데이터 캐싱으로 GitHub REST API Rate Limit 초과 방지 확인.

---

## 📝 5. 결론 및 공학적 인사이트

- **프레임워크 밑바닥 이해**: React의 State-Driven UI 원리를 순수 JS 스토어 패턴으로 완벽하게 체득.
- **안정적 배포**: CI/CD 배포 파이프라인 자동화 구축 🚀
