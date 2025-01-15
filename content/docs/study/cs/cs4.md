---
date : 2025-01-15
weight: 501
tags: ['2025-01']
categories: ['CS']
bookHidden: true
title: "CS"
---

# INSERT, UPDATE, DELETE, SELECT, WHERE

##### 2025-01-15

---

## INSERT

insert는 테이블에 새로운 데이터를 삽입.

debeaver tool 환경설정. auto commit을 해제한다. rollback과 commit 실습을 위함이다. window->perferences->connection type -> auto commit by default를 체크 해제.

insert 쿼리 실습.

sysdate는 지금 현재로 해준다. 

```sql
SELECT * FROM NETFLIX;

INSERT INTO NETFLIX VALUES ('나의 아저씨', '드라마', 50, SYSDATE);

COMMIT;
```

데이터 추가하기. video name, view cnt 값만 있는 데이터를 넣어준다. 

```sql
INSERT INTO NETFLIX (VIDEO NAME, VIEW_CNT) VALUES ('시그널', 42);

COMMIT;
```

commit을 하지 않은 상태에서 rollback을 하면 select 해봤을때 데이터가 사라져있다. 

```sql
INSERT INTO NETFLIX VALUES ('응답하라 1988', '드라마', 25, SYSDATE-30);
INSERT INTO NETFLIX VALUES ('이태원 클라쓰', '드라마', 30, SYSDATE-40);
INSERT INTO NETFLIX VALUES ('미스터 선샤인', '드라마', 22, SYSDATE-300);

COMMIT;
```

select로도 확인할 수 있다. 

```sql
SELECT * FROM NETFLIX;
```

## UPDATE 

insert 한 데이터를 변경한다. 

```sql
SELECT * FROM NETFLIX n;
```
해보면 5개 row에 데이터가 들어있음을 확인.

나의 아저씨 드라마의 조회수 변경. 테이블이름 변경하고싶은 컬럼이름 적어준다. 

```sql
UPDATE NETFLIX SET VIEW_CNT = 70 WHERE VIDEO_NAME = '나의 아저씨';

SELECT * FROM NETFLIX n;
```
select 해보면 view가 50에서 70으로 안바뀜. update도 insert처럼 commit 해줘야한다.

```sql
COMMIT;
```
조회수가 70으로 변경됨. 

insert 문으로 video name, view cnt만 데이터를 넣어줬는데 category와 reg date도 채우기.

```sql
UPDATE NETFLIX SET CATEGORY = '드라마', REG_DATE = TO_DATE('20210101', 'YYYYMMDD'), WHERE VIDEO_NAME = '시그널';

COMMIT;

SELECT * FROM NETFLIX n;
```

확인해보면 시그널 row에 컬럼이 모두 채워져있다.

롤백 시켜주기. 

주의할점은 WHERE 조건절을 빼먹으면 모든 데이터가 변경된다.

## DELETE

DELETE는 테이블의 데이터를 삭제. truncate는 테이블의 모든 데이터삭제, delete는 원하는 데이터만 삭제. truncate는 되돌릴수없고 delete는 rollback 가능. 모든 데이터 삭제할때는 truncate가 빠르다.

netflix 테이블의 데이터 전체조회

```sql
SELECT * FROM NETFLIX n;
```
video name 컬럼이 '미스터 선샤인'인 데이터 제거.

```sql
DELETE FROM NETFLIX n WHERE VIDEO_NAME = '미스터 선샤인';

COMMIT;

SELECT * FROM NETFLIX n;
```

조건을 길게 주기. 카테고리가 드라마이면서 view cnt가 35보다 작은 데이터 삭제. 

```sql
DELETE FROM NETFLIX n WHERE CATEGORY = '드라마' AND VIEW_CNT < 35;

COMMIT;

SELECT * FROM NETFLIX n;
```

rollback 하기

```sql
ROLLBACK;

SELECT * FROM NETFLIX n;
```

없어졌다.

시그널이랑 나의 아저씨 둘다지우기. (그리고 n 안써도됨)

```sql
DELETE FROM NETFLIX WHERE VIDEO_NAME IN ('시그널', '나의 아저씨');

COMMIT;

SELECT * FROM NETFLIX;
```

where를 안쓰면 truncate와 같은 기능.

```sql
DELETE FROM NETFLIX;
```

## SELECT

select문은 데이터를 조회하는 쿼리.

테이블의 전체 데이터 조회.

```sql
SELECT * FROM NETFLIX;
```

```sql
SELECT VIDEO_NAME, CATEGORY, VIEW_CNT, REG_DATE FROM NETFLIX;
```

REG_DATE 필요없으면 아래와 같이 select 할수도있다.

```sql
SELECT VIDEO_NAME, CATEGORY, VIEW_CNT FROM NETFLIX;
```
조건절 써주기. video name이 '나의아저씨'인 데이터 조회 / 아닌 데이터 조회

```sql
SELECT * FROM NETFLIX WHERE VIDEO_NAME = '나의 아저씨';

SELECT * FROM NETFLIX WHERE VIDEO_NAME <> '나의 아저씨';
```
reg date가 최근 한달인 데이터 조회
``` sql
SELECT * FROM NETFLIX WHERE REG_DATE > SYSDATE-30;
```
category가 여러 데이터에서 중복인경우 하나만 보고싶을때
```sql
SELECT DISTINCT CATEGORY FROM NETFLIX;
```

## WHERE

카테고리가 애니메이션인 데이터 조회
```sql
SELECT * FROM NETFLIX WHERE CATEGORY = '애니메이션';
```
카테고리가 애니메이션이거나 영화인 데이터 조회
```sql
SELECT * FROM NETFLIX WHERE CATEGORY IN ('애니메이션', '영화');
```
카테고리가 애니메이션도 아니고 영화도 아닌 데이터 조회
```sql
SELECT * FROM NETFLIX WHERE CATEGORY NOT IN ('애니메이션', '영화');
```
범위 조건 주기.
```sql
SELECT * FROM NETFLIX WHERE VIEW_CNT < 70;

SELECT * FROM NETFLIX WHERE VIEW_CNT <= 70;

SELECT * FROM NETFLIX WHERE REG_DATE < TO_DATE('20210101', 'YYYYMMDD')
```

이렇게하면 2020년에 등록된 데이터만 조회.








### 강의 출처

**SQL전문가 정미나** https://youtube.com/playlist?list=PLyQR2NzLKOCa5UujnJIFR7wOVOD0lS6EB&si=uVBP3HZWcrYbiD1K
