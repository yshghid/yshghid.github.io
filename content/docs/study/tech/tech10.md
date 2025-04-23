---
date : 2025-04-22
tags: ['2025-04']
categories: ['sql']
bookHidden: true
title: "변환형/NULL관련 함수"
---

# 변환형/NULL관련 함수

## 목록

*2025-04-23* ⋯ [섹션 4. 함수](https://yshghid.github.io/docs/study/tech/tech9/#%ec%84%b9%ec%85%98-4-%ed%95%a8%ec%88%98)

---

## 섹션 4. 함수

### 1. 변환형 함수

#1

#oracle

```sql
SELECT
	to_number('11'),
	to_date('2024.06.11', 'YYYY.MM.DD'),
	to_char(sysdate, 'hh mi ss'),
	to_char(10)
FROM dual
```

![image](https://github.com/user-attachments/assets/fff7907e-09f9-4c24-99a6-1696e2c882ff)

- to number: 문자형을 숫자형으로
- to date: 문자형을 날짜형으로
- to char: 숫자형이나 날짜형을 문자형으로
  - to_char(sysdate, 'hh mi ss') -> 시, 분, 초
  - to_char(sysdate, 'YYYY-MM-DD') -> 년, 월, 일

#sql

```sql
select
  convert(varchar, 20),
  cast(20 AS varchar(2)),
  cast('20.99' as float),
  convert(float, '20.99'),
  cast(getdate() AS varchar),
  convert(varchar, getdate(), 14),

  cast(cast(getdate() AS char) as datetime)
  convert(datetime, cast(getdate() AS char))
```

- convert varchar 20: 숫자 20을 문자형으로 변환 -> '20'
- cast 20 as varchar 2: 숫자 20을 길이 2의 문자형으로 변환 -> '20'
- cast '20.99' as float: 문자 '20.99'를 실수형으로 변환 -> 20.99
  - convert float '20.99': cast '20.99' as float와 동일

- convert varchar getdate 14: 현재 시간만 추출해서 문자형 변환 -> '16:23:15:123' (hh:mi:ss:mmm)

- cast (cast (getdate as char) as datetime): 날짜를 문자열로 바꿨다가 다시 날짜형으로 변환
  - cast(getdate() AS char): 날짜를 문자열로 변환
  - cast (getdate as char) as datetime: cast (getdate as char)이라는 결과에 컬럼이름을 datetime으로
  - getdate() AS char: getdate()이라는 결과에 컬럼이름을 char으로
  
  - convert(datetime, cast(getdate() AS char)): cast (cast (getdate as char) as datetime)와 동일






> 강의 출처 https://www.inflearn.com/course/sqld-%EC%99%84%EC%84%B1-2%EA%B3%BC%EB%AA%A9
