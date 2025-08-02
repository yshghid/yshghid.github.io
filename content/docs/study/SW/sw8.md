---
date : 2025-07-29
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "SQL #2 학사 관리 시스템 설계 - 스키마 분리 및 멀티 프로젝트 설계"
---

# SQL #2 학사 관리 시스템 설계 - 스키마 분리 및 멀티 프로젝트 설계

#2025-07-29

---

### 1. 문제

이전에 만든 ERD를 기반으로 PostgreSQL 로 스키마 분리 및 멀티 프로젝트 설계합니다.
- 주제
  . 서울캠퍼스/제주캠퍼스별 학사 관리 시스템 (Learning Management System)
 동일한 학사관리 시스템 구조를 기반으로, 캠퍼스에 따라 데이터를 스키마 단위로
 분리 설계하고 향후 AI 분석 결과의 멀티 벡터 저장 구조로 확장 가능하도록 구조 설계
- 요구사항
	. 교육과정, 수강생 과정운영자, 강사, 과정 설명 텍스트, Review 등으로 구성하되,
	 캠퍼스별 특성을 고려하여 스키마 분리
	. 서울 캠퍼스와 제주 캠퍼스 간 교수/강사/과정은 중복될 수 있음

### 2. 서울캠퍼스/제주캠퍼스별 학사 관리 시스템 엔티티 도출

#기존 스키마

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

#새로설계된스키마

```plain text
Schemas:
- public
  └── instructors
      - id (integer, PK)
      - name (varchar)
      - email (varchar)
      - created_at (datetime)
  
  └── courses
      - id (integer, PK)
      - title (varchar)
      - instructor_id (integer, FK → public.instructors.id)
      - created_at (datetime)
  
  └── course_descriptions
      - course_id (integer, PK, FK → public.courses.id)
      - description (text)
- seoul
  └── students
      - id (integer, PK)
      - name (varchar)
      - email (varchar)
      - created_at (datetime)
  
  └── enrollments
      - student_id (integer, FK → seoul.students.id)
      - course_id (integer, FK → public.courses.id)
      - enrollment_date (varchar)
      - Primary Key: (student_id, course_id)
  
  └── reviews
      - id (integer, PK)
      - student_id (integer, FK → seoul.students.id)
      - course_id (integer, FK → public.courses.id)
      - comment (text)
      - created_at (datetime)
- jeju
  └── students
      - id (integer, PK)
      - name (varchar)
      - email (varchar)
      - created_at (datetime)
  
  └── enrollments
      - student_id (integer, FK → jeju.students.id)
      - course_id (integer, FK → public.courses.id)
      - enrollment_date (varchar)
      - Primary Key: (student_id, course_id)
  
  └── reviews
      - id (integer, PK)
      - student_id (integer, FK → jeju.students.id)
      - course_id (integer, FK → public.courses.id)
      - comment (text)
      - created_at (datetime)
- analytics
  └── student_embeddings
      - campus (varchar)
      - student_id (integer)
      - embedding (vector)
      - updated_at (datetime)
  
  └── course_vectors
      - course_id (integer)
      - vector (vector)
      - updated_at (datetime)
```

### 3. ERD 작성

dbdiagram 사용 https://dbdiagram.io/

```plain text
// === public 스키마 ===
Table public.instructors {
  id         integer [primary key]
  name       varchar
  email      varchar
  created_at datetime
}

Table public.courses {
  id            integer [primary key]
  title         varchar
  instructor_id integer [ref: > public.instructors.id]
  created_at    datetime
}

Table public.course_descriptions {
  course_id   integer [primary key, ref: > public.courses.id]
  description text
}

// === seoul 스키마 ===
Table seoul.students {
  id         integer [primary key]
  name       varchar
  email      varchar
  created_at datetime
}

Table seoul.enrollments {
  student_id      integer [ref: > seoul.students.id]
  course_id       integer [ref: > public.courses.id]
  enrollment_date varchar
  Primary Key(student_id, course_id)
}

Table seoul.reviews {
  id         integer [primary key]
  student_id integer [ref: > seoul.students.id]
  course_id  integer [ref: > public.courses.id]
  comment    text
  created_at datetime
}

// === jeju 스키마 ===
Table jeju.students {
  id         integer [primary key]
  name       varchar
  email      varchar
  created_at datetime
}

Table jeju.enrollments {
  student_id      integer [ref: > jeju.students.id]
  course_id       integer [ref: > public.courses.id]
  enrollment_date varchar
  Primary Key(student_id, course_id)
}

Table jeju.reviews {
  id         integer [primary key]
  student_id integer [ref: > jeju.students.id]
  course_id  integer [ref: > public.courses.id]
  comment    text
  created_at datetime
}

// === analytics 스키마 ===
Table analytics.student_embeddings {
  campus     varchar
  student_id integer
  embedding  varchar
  updated_at datetime
  Note: 'AI 분석용 임베딩 정보'
}

Table analytics.course_vectors {
  course_id  integer
  vector     varchar
  updated_at datetime
  Note: '강의 임베딩 벡터'
}
```

<img width="1363" height="842" alt="image" src="https://github.com/user-attachments/assets/1b1ea01e-eb11-4929-80fe-cfa91631a7d3" />

### 4. PostgreSQL에 생성

```plain text
-- public 테이블 생성
CREATE TABLE public.instructors (
  id SERIAL PRIMARY KEY,
  name VARCHAR,
  email VARCHAR,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.courses (
  id SERIAL PRIMARY KEY,
  title VARCHAR,
  instructor_id INTEGER REFERENCES public.instructors(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.course_descriptions (
  course_id INTEGER PRIMARY KEY REFERENCES public.courses(id),
  description TEXT
);

-- seoul 스키마 및 테이블 생성
CREATE SCHEMA seoul;

CREATE TABLE seoul.students (
  id SERIAL PRIMARY KEY,
  name VARCHAR,
  email VARCHAR,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE seoul.enrollments (
  student_id INTEGER REFERENCES seoul.students(id),
  course_id INTEGER REFERENCES public.courses(id),
  enrollment_date VARCHAR,
  PRIMARY KEY (student_id, course_id)
);

CREATE TABLE seoul.reviews (
  id SERIAL PRIMARY KEY,
  student_id INTEGER REFERENCES seoul.students(id),
  course_id INTEGER REFERENCES public.courses(id),
  comment TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- jeju 스키마 및 테이블 생성
CREATE SCHEMA jeju;

CREATE TABLE jeju.students (
  id SERIAL PRIMARY KEY,
  name VARCHAR,
  email VARCHAR,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE jeju.enrollments (
  student_id INTEGER REFERENCES jeju.students(id),
  course_id INTEGER REFERENCES public.courses(id),
  enrollment_date VARCHAR,
  PRIMARY KEY (student_id, course_id)
);

CREATE TABLE jeju.reviews (
  id SERIAL PRIMARY KEY,
  student_id INTEGER REFERENCES jeju.students(id),
  course_id INTEGER REFERENCES public.courses(id),
  comment TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- analytics 스키마 및 테이블 생성
CREATE SCHEMA analytics;

CREATE TABLE analytics.student_embeddings (
  campus VARCHAR,
  student_id INTEGER,
  embedding VARCHAR,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE analytics.course_vectors (
  course_id INTEGER,
  vector VARCHAR,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3b912412-8459-4e8a-a334-7660b9604e88" />

#
