---
date : 2025-04-22
tags: ['2025-04']
categories: ['sql']
bookHidden: true
title: "문자형/숫자형/날짜형/기타 함수"
---

# 문자형/숫자형/날짜형/기타 함수

## 목록

*2025-04-22* ⋯ [섹션 4. 함수](https://yshghid.github.io/docs/study/tech/tech9/#%ec%84%b9%ec%85%98-4-%ed%95%a8%ec%88%98)

---

## 섹션 4. 함수

### 1. 문자형 함수

#1

```sql
SELECT
	lower('SQL Expert'),
	upper('SQL Expert'),
	ascii('A'),
	chr(65),
	concat('SQL', ' Expert'), -- 2개까지만
	length('SQL Expert')
FROM dual;
```
![image](https://github.com/user-attachments/assets/f6382c39-6f5e-4bfa-ac09-be164274db09)

- 각각 이렇게 나온다.
  - oracle은 concat 2개까지만, sql server는 3개도 됨.
  - oracle은 length이고 sql은 len이다.

```sql
SELECT
	ltrim('xxxYYYZZxYZ', 'x'),
	ltrim('xxxYYYZZxYZ', 'xY'),
	ltrim('xxxYYYZZxYZ', 'xYZ'),
	ltrim('   xxxx'),
	rtrim('xxxYYYZZxYZ', 'ZY'),
	rtrim('xxxx    '),
	trim('  xxxx    ')
FROM dual;
```

![image](https://github.com/user-attachments/assets/968759ed-2a71-4568-b321-a49843197f43)

- ltrim: 왼쪽부터 검사해서 'x' 제거된 문자열을 반환
  - 'xY' 넣어주면  'x'와 'Y'가 모두 제거
  - 아무것도 안넣어주면 공백 제거

- sql 서버에서는?
  - ltrim이 두번째 인자를 받지않는다.
  - LTRIM과 RTRIM이 오직 공백만 제거

```sql
SELECT
	trim('x' FROM 'xxxxYYYzzYZxxxx'),
	trim(BOTH 'x' FROM 'xxxxYYYzzYZxxxx'),
	trim(LEADING 'x' FROM 'xxxxYYYzzYZxxxx'),
	trim(TRAILING 'x' FROM 'xxxxYYYzzYZxxxx')
FROM dual;
```

![image](https://github.com/user-attachments/assets/f74b99b6-c079-4d04-ae1d-eed37c7ca15a)


- trim은 ltrim rtrim과 달리 문자열 하나밖에 못넣어준다.
- `trim('x' FROM 'xxxxYYYzzYZxxxx')`은 사실 앞에 BOTH가 생략된거랑 같다.
- leading trailing은 ltrim rtrim과 사실상 같다.

#2

```sql
SELECT
	REPLACE('sql expert','ert', 'tre')
FROM dual;
```


- REPLACE: 문자열을 찾아서 치환해줘서 `sql exptre`가 나옴
  - 빈문자열을 넣으면 그냥 삭제해준다.

```sql
SELECT
	substr('Gangneung Wonju', 8, 4)
FROM dual;
```

- substr: 8번째부터 4개 출력해줘서 `g Wo`가 나옴
  - sql에서는 이름이 substring이다.
  - oracle은 `substr('Gangneung Wonju', 8)`도 되는데 sql은 세번째인자 생략하면 안된다.

```sql
SELECT
	left('Gangneung Wonju', 8)
	right('Gangneung Wonju', 8)
FROM dual;
```

- sql에서만 되고 left는 1번째부터 8번째까지 출력해줘서 `Gangneun`가 나오고 right는 `ng Wonju` 나옴
  - oracle에서 right와 동일한결과는 `substr('Gangneung Wonju', -8)`
  - oracle에서 `substr('Gangneung Wonju', -5, 2)` 하면 `Wo` 나옴


### 2. 숫자형 함수

#1 

```sql
SELECT 
	mod(7,3),
	sign(-3),
	ABS(-15),
	ceil(38.123),
	floor(38.888),
	round(38.525, 2)
FROM dual
```
![image](https://github.com/user-attachments/assets/a01292d7-12ff-4447-9fa1-6639ced3dc67)

- mod는 나머지, sign은 부호
  - sql은 `mod(7,3)`대신 `7%3`으로쓴다.
  - ceil 대신 ceiling 쓰고 floor은 그대로 쓴다.
- round(38.525, 2)는 소수점 둘째자리까지 반올림한다.
  - oracle은 round(38.525)와 같이 두번째 인자 생략 가능하다.
  - `round(38.525, -1)`하면 십의자리까지 반올림해서 40이 된다

#2

```sql
SELECT 
	trunc(38.888, 2),
	round(38.888, 2),
	ceil(38.888),
	floor(38.888)
FROM dual
```

![image](https://github.com/user-attachments/assets/65cbda39-ad64-49de-ba93-791f06e459dd)

- floor, ceil은 두번째 인자 못받는데 trunc는 받는다
  - trunc는 버림이고 round는 반올림이다.
  - sql은 trunc 없고 `round(38.525, 2)`해주면 38.89가, `round(38.525, 2, 1)` 해주면 버림으로 38.88이 나온다

```sql
SELECT
	power(2, 4),
	exp(2),
	sqrt(4),
	log(10,100),
	ln(7.3890560989306502272304274605750078132)
FROM dual
```

![image](https://github.com/user-attachments/assets/8df6dde9-bf11-4c3e-b22a-bec78c3ca4fa)

- exp(2)는 e^2
- power(2,4)는 2^4
- sqrt(4)는 루트4
- log(10,100)은 log_(10) 100 = 2
- ln(e^2)는 2
  - sql은 ln 함수가없고 자연상수e를 밑으로갖는 로그는 `log(7.3890560989306502272304274605750078132)`처럼 두번째인자를 안넣어주면된다.

#3

#sql

```sql
SELECT
	DAY(GETDATE()),
	MONTH(GETDATE()),
	YEAR(GETDATE()),
	DATEPART(DAY,GETDATE()),
	GETDATE())
```

#oracle
```sql
SELECT 
	TO_NUMBER(TO_CHAR(SYSDATE,'DD')),
	extract(DAY FROM SYSDATE),
	extract(YEAR FROM SYSDATE),
	SYSDATE
FROM dual
```
![image](https://github.com/user-attachments/assets/7975b3e8-be73-4e01-8641-b8b60063ba09)



> 강의 출처 https://www.inflearn.com/course/sqld-%EC%99%84%EC%84%B1-2%EA%B3%BC%EB%AA%A9
