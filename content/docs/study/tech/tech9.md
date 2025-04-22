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



> 강의 출처 https://www.inflearn.com/course/sqld-%EC%99%84%EC%84%B1-2%EA%B3%BC%EB%AA%A9
