---
date : 2025-04-22
tags: ['2025-04']
categories: ['sql']
bookHidden: true
title: "DBeaver 환경설정, SELECT문"
---

# DBeaver 환경설정, SELECT문

## 목록

*2025-04-22* ⋯ [섹션 1. SQLD 시험 개요, 강의 소개, 실습 환경 설정](https://yshghid.github.io/docs/study/tech/tech7/#%ec%84%b9%ec%85%98-1-sqld-%ec%8b%9c%ed%97%98-%ea%b0%9c%ec%9a%94-%ea%b0%95%ec%9d%98-%ec%86%8c%ea%b0%9c-%ec%8b%a4%ec%8a%b5-%ed%99%98%ea%b2%bd-%ec%84%a4%ec%a0%95)

*2025-04-22* ⋯ [섹션 3. SELECT 문](https://yshghid.github.io/docs/study/tech/tech7/#%ec%84%b9%ec%85%98-3-select-%eb%ac%b8)

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

- DBeaver 열기
  - JDBC URL Template, Username, Password 입력
  - Driver setting > Libraries > /Users/yshmbid/oracle/ojdbc8-full 넣어줌

- Test Connection했을때 아래처럼 뜨면 정상!

![image](https://github.com/user-attachments/assets/846626b4-ae67-4f75-a9cd-c0fe5e765319)

### 2. 데이터세트 소개


![image](https://github.com/user-attachments/assets/7c0e3b2f-b89f-4baf-a8d5-2622923b2439)


![image](https://github.com/user-attachments/assets/80c6bbdd-d2a6-4d7f-9747-dbcb21071277)

- 부서,사원 데이터셋
- 축구 데이터셋
 - stadium은 여러개의 team 데이터셋을 가질수있다. (삼지창)
   - stadium에는 team이 없는 경우도 있다. (optional) team은 반드시 경기장이 있어야하고 하나만 가질수있다.
   - team은 여러명의 player를 가질수있고 0명의 player를 가져도된다(흰색원). player는 반드시 team을 하나 가져야한다.
 
 - stadium은 경기가 여러개일수있고 경기가 없을수도 있다. schedule은 경기장을 하나만 가질수있다.
   - schedule에 stadium_id랑 sche_date는 여러개의키를 쓰는 복합키이다.

- 불러오는법

```sql
SELECT * FROM sqld.emp;
SELECT * FROM sqld.dept;

SELECT * FROM sqld.stadium;
```

---

## 섹션 3. SELECT 문

### 1. 스키마

```sql
SELECT * FROM emp;
```

- `sqld.`를 앞에 안붙이려면 스키마를 SQLD로 바꿔줘야한다.

### 2. 실습

#1

```sql
-- *는 모든 열 선택
SELECT * FROM emp;

-- 조회하려는 컬럼을 콤마(,)로 구분해서 가져오기
SELECT empno, ename, job, deptno
FROM emp;
```

#2

```sql
SELECT ALL
	job
FROM emp;

SELECT DISTINCT
	job
FROM emp;
```

- 두 쿼리의 차이?
  - ALL은 중복 포함, DISTINCT하면 중복 제거. 

```sql
SELECT DISTINCT
	deptno, job
FROM emp
ORDER BY 1, 2;
```
![image](https://github.com/user-attachments/assets/7203a921-68dd-465f-a7ce-5fbc23d6aa97)

- DEPTNO와 JOB의 모든 조합을 가져온다

#3

```sql
-- ALIAS 부여하기
-- AS 키워드로 컬럼에 별명 부여
SELECT 
    empno AS 사원번호,
	ename AS 이름,
	deptno AS 부서번호,
	job AS 업무
FROM emp;
```

![image](https://github.com/user-attachments/assets/17953623-7ffb-4042-98c1-28a202372323)

- 출력되는 컬럼 label이 변경.
  - AS 생략가능
  - 띄어쓰기, 특수문자 안됨, 대소문자 구별안됨

#4

```sql
-- 산술 연산자, 수학의 사칙연산
-- NUMBER와 DATE에 적용 가능
-- 연산자 우선순위
-- 1. ()
-- 2. *, /
-- 3. +, -

SELECT sal,
	   sal*0.3,
	   100+300, -- 모든 행에 같은 값
	   sal-deptno
FROM emp;
```
![image](https://github.com/user-attachments/assets/3e089338-8e3a-435f-b6e9-3a806bcf611b)

```sql
-- NULL과의 산술 연산은 항상 NULL을 반환
SELECT sal+comm,
	   sal+NULL,
	   sal-NULL,
	   sal*NULL,
	   sal/NULL
FROM emp;
```
![image](https://github.com/user-attachments/assets/b073b98d-e436-4831-b988-801289a81eca)


#5

- 합성 연산자는 문자열 결합에 사용.

```sql
/* 
 * 합성 연산자
 * 오라클 ||, SQL Server +
 */
-- KING의 직책은 PRESIDENT이며 연봉은 5000이다.

SELECT ename || '의 직책은 ' || job || '이며 연봉은 ' || sal ||'이다.'
FROM emp;
```

![image](https://github.com/user-attachments/assets/7b53539e-2275-404c-afa2-9f66bcdcdaca)

```sql
-- CONCAT 2개 문자열 합성
SELECT CONCAT('연봉', sal)
	-- CONCAT('연봉', ' ', sal) -- 오류. Oracle CONCA은 인자 2개만 받음 
FROM emp;
```

- CONCAT도 문자열을 합성하는 함수인데 2개만 가능하다. 

![image](https://github.com/user-attachments/assets/b6048927-571b-4010-bfd6-6b2b950953dd)





> 강의 출처 https://www.inflearn.com/course/sqld-%EC%99%84%EC%84%B1-2%EA%B3%BC%EB%AA%A9

