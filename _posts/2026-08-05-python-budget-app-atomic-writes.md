---
layout: post
title: "💰 파이썬 순수 표준 라이브러리로 튼튼한 파일 가계부(Atomic Write) 만들기"
date: 2026-08-05 16:20:00 +0900
tags: [Python, Architecture, FileIO, AtomicWrite, Codyssey]
category: Codyssey-Mission
---

파이썬 표준 라이브러리만을 사용하여 계층화 아키텍처(Model-Repository-Service-CLI)와 원자적 쓰기(Atomic Write)를 구현한 파일 가계부 서비스 개발기입니다.

임시 파일(`.tmp`) 기록 후 `os.replace` 원자적 교체를 수행하여 전원이 끊겨도 데이터 파일 손상을 철저히 방지했습니다! 🎈
