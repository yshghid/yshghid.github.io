---
date : 2025-04-22
tags: ['2025-04']
categories: ['sql']
bookHidden: true
title: "DESeq2 워크플로우"
---

# [SQLD] DBeaver 환경설정, SELECT문

## 목록

*2025-04-22* ⋯ 섹션 1. SQLD 시험 개요, 강의 소개, 실습 환경 설정

*2025-04-22* ⋯ 섹션 3. SELECT 문

---

## 섹션 1. SQLD 시험 개요, 강의 소개, 실습 환경 설정

### 1. 실습환경 설정

- DBeaver Community 설치 https://dbeaver.io/download/](https://dbeaver.io/download/)
- Wallet 다운로드
- JDBC Driver 다운로드 https://www.oracle.com/kr/database/technologies/appdev/jdbc-downloads.html

```bash
$ pwd
/Users/yshmbid/oracle

$ ls
Wallet_SQLD		ojdbc8-full
Wallet_SQLD.zip		ojdbc8-full.tar.gz
```

- `/Users/yshmbid/oracle` 위치에 잘 넣어줬다

- DBeaver 열고 JDBC URL Template, Username, Password 입력
- Driver setting > Libraries > /Users/yshmbid/oracle/ojdbc8-full 넣어줌

- Test Connection했을때 아래처럼 뜨면 정상!

![image](https://github.com/user-attachments/assets/846626b4-ae67-4f75-a9cd-c0fe5e765319)

### 2. 데이터세트 소개

1. 부서,사원 데이터셋
![image](https://github.com/user-attachments/assets/7c0e3b2f-b89f-4baf-a8d5-2622923b2439)

2. 축구 데이터셋
![image](https://github.com/user-attachments/assets/80c6bbdd-d2a6-4d7f-9747-dbcb21071277)

 - stadium은 여러개의 team 데이터셋을 가질수있다. (삼지창)
   - stadium에는 team이 없는 경우도 있다. (optional) team은 반드시 경기장이 있어야하고 하나만 가질수있다.
   - team은 여러명의 player를 가질수있고 0명의 player를 가져도된다(흰색원). player는 반드시 team을 하나 가져야한다.
 
 - stadium은 경기가 여러개일수있고 경기가 없을수도 있다. schedule은 경기장을 하나만 가질수있다.
   - schedule에 stadium_id랑 sche_date는 여러개의키를 쓰는 복합키이다.

- 불러오는법

```sql
SELECT * FROM sqld.emp;
SELECT * FROM sqld.dept;

---

## 섹션 3. SELECT 문


SELECT * FROM sqld.stadium;
```
