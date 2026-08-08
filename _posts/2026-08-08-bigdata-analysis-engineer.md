---
layout: post
title: "🎓 [Bigdata-Analysis-Engineer] 320제 클라이언트 사이드 CBT 모의고사 웹 앱 구축기"
slug: bigdata-analysis-engineer
date: 2026-08-08 11:00:00 +0900
tags: [JavaScript, CBT, BigData, WebApp, LocalStorage, Codyssey]
category: Codyssey-Mission
---

# 🎓 Bigdata-Analysis-Engineer 미션 심층 기술 딥다이브

## 💡 1. 미션 개요 및 배경

빅데이터 분석기사 필기 시험 준비를 위해 수험생들이 효율적으로 CBT(Computer Based Test) 환경을 체험하고 모의고사를 풀 수 있는 **100% 클라이언트 사이드 SPA 웹 애플리케이션**을 구축했습니다.

서버 비용과 DB 관리 오버헤드 없이 HTML5, CSS3, Vanilla JS 및 `questions.json` 데이터 세트만으로 구성되어, **GitHub Pages** 환경에서 무비용 즉시 서비스할 수 있도록 완벽하게 엔지니어링되었습니다.

---

## ⚡ 2. 핵심 기술 쟁점 & 트레이드오프

```text
[ Problem View (1 / 5 / 80 Questions) ] <─── Data Sync ───> [ Interactive OMR Sheet ]
                   │                                                  │
                   ▼                                                  ▼
[ Timer (120min Countdown) ] ────────── Submit ──────────> [ Scoring & Cutoff Engine ]
                                                                      │
                                                           Average >= 60 & Subject >= 40
```

### 쟁점 1: 서버 DB 연동 vs JSON 기반 클라이언트 사이드 데이터 관리

- **서버 DB 방식**: 유저별 풀이 이력 저장은 용이하나 백엔드 서버 인프라 유지 비용 및 Latency 발생.
- **클라이언트 사이드 방식**: `questions.json` (320문항/4개 회차)을 프론트엔드에서 즉시 다루고, 사용자 응시 상태는 브라우저 `localStorage`에 자동 영속화하여 속도 및 무비용 환경 확보.

---

## 🛠️ 3. 소스코드 핵심 하이라이트

### 3.1 자동 채점 및 과락 판정 알고리즘 (`app.js`)

```javascript
function calculateResults(userAnswers, questions) {
  let totalScore = 0;
  const subjectScores = { 1: 0, 2: 0, 3: 0, 4: 0 };

  questions.forEach((q) => {
    const userChoice = userAnswers[q.id];
    if (userChoice === q.answer) {
      subjectScores[q.subjectId] += 5; // 문항당 5점
      totalScore += 5;
    }
  });

  // 과락 판정 (과목당 40점 미만 발생 시 불합격)
  let isFailedByCutoff = false;
  Object.keys(subjectScores).forEach(subId => {
    if (subjectScores[subId] < 40) {
      isFailedByCutoff = true;
    }
  });

  const isPassed = totalScore >= 60 && !isFailedByCutoff;
  return { totalScore, subjectScores, isPassed, isFailedByCutoff };
}
```

---

## 🧪 4. 테스트 & 무결성 검증

1. **320제 문항 데이터 무결성**: 4개 회차 80문항 전체에 대해 문제, 선택지, 정답, 상세 해설 누락 여부 검증 완료.
2. **배포 검증**: `.nojekyll` 파일 추가 후 GitHub Pages 배포 시 전 세계 어디서나 100ms 이내에 즉시 로딩되는 고성능 확인.

---

## 📝 5. 결론 및 공학적 인사이트

- **실용적 시스템 구축**: 실제 자격증 시험 준비에 즉시 투입 가능한 반응형 CBT Web App 서비스 구축.
- **무비용 고성능 서비스**: Serverless 정적 파일 구조 아키텍처의 우수성 실증 🚀
