---
date : 2025-07-29
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "SQL #1 학사 관리 시스템 설계 - 엔터티 도출 및 ERD 작성"
---

# SQL #1 학사 관리 시스템 설계 - 엔터티 도출 및 ERD 작성

#2025-07-29

---

### 1. 문제

AI 기반 학사 관리 시스템 (Learning Management System) 설계를 위한 엔터티 도출 및 ERD 작성 실습입니다.
- 요구사항
	. 교육과정, 수강생, 과정운영자, 강사, 과정 설명 텍스트, Review 등으로 구성
	. 과정 설명 텍스트는 향후 AI 임베딩 대상이므로 충분한 길이와 자유 텍스트로 정의
	
- 순서
	. 학사관리시스템 엔티티 도출 및 검증
	. ERD 변환 작업
	. 변환된 ERD로 설치된 PostgreSQL 에 생성

###

### 2. 학사관리시스템 엔티티 도출

<mark>#구조</mark>

```plain text
Schemas:
- public
  └── students
  └── instructors
  └── courses
  └── course_descriptions
  └── enrollments
  └── reviews
```

###

### 3. ERD 작성

dbdiagram 사용 https://dbdiagram.io/

```plain text
Table students {
  id         integer [primary key]
  name       varchar
  email      varchar
  created_at datetime
}

Table instructors {
  id         integer [primary key]
  name       varchar
  email      varchar
  created_at datetime
}

Table courses {
  id           integer [primary key]
  title        varchar
  instructor_id integer [ref: > instructors.id]
  created_at   datetime
}

Table course_descriptions {
  course_id   integer [primary key, ref: > courses.id]
  description text
}

Table enrollments {
  student_id      integer [ref: > students.id]
  course_id       integer [ref: > courses.id]
  enrollment_date varchar

  Indexes {
    (student_id, course_id) [unique]
  }
}

Table reviews {
  id         integer [primary key]
  student_id integer [ref: > students.id]
  course_id  integer [ref: > courses.id]
  comment    text
  created_at datetime
}
```

<img width="1279" height="632" alt="image" src="https://github.com/user-attachments/assets/d95c2c46-ca73-4eca-aebe-f840a22cc785" />

###

### 4. PostgreSQL에 생성


```sql
-- 학생 테이블
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR,
    email VARCHAR,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 강사 테이블
CREATE TABLE instructors (
    id SERIAL PRIMARY KEY,
    name VARCHAR,
    email VARCHAR,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 강의 테이블
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    title VARCHAR,
    instructor_id INTEGER REFERENCES instructors(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 강의 설명 테이블
CREATE TABLE course_descriptions (
    course_id INTEGER PRIMARY KEY REFERENCES courses(id),
    description TEXT
);

-- 수강 신청 테이블 (복합 기본키는 인덱스로 대체 가능)
CREATE TABLE enrollments (
    student_id INTEGER REFERENCES students(id),
    course_id INTEGER REFERENCES courses(id),
    enrollment_date VARCHAR,
    PRIMARY KEY (student_id, course_id)
);

-- 강의 리뷰 테이블
CREATE TABLE reviews (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES students(id),
    course_id INTEGER REFERENCES courses(id),
    comment TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3114510a-6f41-4a56-9d8d-d18e4f7c4b02" />

###

cf) 원래 데이터, 스키마 지우기

```sql
DROP SCHEMA IF EXISTS analytics CASCADE;
DROP SCHEMA IF EXISTS jeju CASCADE;
DROP SCHEMA IF EXISTS seoul CASCADE;
DROP SCHEMA IF EXISTS public CASCADE;

-- 기본 public 스키마 재생성
CREATE SCHEMA public;
```

#


