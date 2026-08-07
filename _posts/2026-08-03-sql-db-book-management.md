---
layout: post
title: "🗄️ 표준 SQL과 SQLite로 1:N 참조 무결성 도서 관리 시스템 DB 구축하기 (sql-db)"
date: 2026-08-03 18:00:00 +0900
tags: [SQL, SQLite, Database, ERD, Codyssey]
category: Codyssey-Mission
---

백엔드 프레임워크나 ORM 라이브러리에 의존하지 않고, 표준 SQL과 SQLite를 활용하여 **도서 관리 시스템(Book Management System)** 데이터베이스를 구축하고 분석한 프로젝트입니다! 📊

---

### 🏗️ 도메인 모델링 & 1:N 관계 3대장

- `MEMBERS (1) : RENTALS (N)` — 한 회원이 여러 번 대여 수행
- `BOOKS (1) : RENTALS (N)` — 한 도서가 여러 번 대여 가능
- `CATEGORIES (1) : BOOKS (N)` — 한 카테고리에 여러 도서 포함

```sql
-- DDL 스크립트 예시 (schema.sql)
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    category_id INTEGER NOT NULL,
    FOREIGN KEY (category_id) REFERENCES categories(category_id) ON DELETE RESTRICT
);
```

파이썬 자동화 스크립트(`generate_results.py`)와 쉘 스크립트(`run.sh`)를 결합하여 16선 핵심 분석 쿼리 실행 리포트를 자동으로 생성하는 파이프라인을 구축했습니다! 🗄️
