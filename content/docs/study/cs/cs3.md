---
date : 2025-01-05
weight: 501
tags: ['2025-01']
categories: ['CS']
bookHidden: true
title: "CS"
---

# sql 전문가 정미나

##### 2025-01-05

## 목록

- CREATE, ALTER, DROP/TRUNCATE [>>](https://yshghid.github.io/docs/study/cs/cs3/#create-alter-droptruncate)
- INSERT, UPDATE, DELETE, SELECT, WHERE [>>](https://yshghid.github.io/docs/study/cs/cs3/#insert-update-delete-select-where)

https://www.youtube.com/playlist?list=PLyQR2NzLKOCa5UujnJIFR7wOVOD0lS6EB

**원래 돈 받고 팔던 강의**
![image](https://github.com/user-attachments/assets/d36d3939-e867-47c2-8099-ae515ec6a3d5)

---

## CREATE, ALTER, DROP/TRUNCATE

**데이터베이스란?**

데이터는 어딘가에 기록되어 있다. 중요한 데이터는 암호화해서 저장한다.

sql은 데이터를 다루는데 사용되는 언어. 

**테이블이란?**

테이블은 데이터베이스를 구성. 각 테이블은 기능이 갖고 정보를 저장하고 있다.

특정 용도 데이터를 담기 위한 테이블은 entity. 

테이블을 구성하는 속성은 column. attribute라고도 한다. 한사람의 데이터를 의미하는 행은 row. record, tuple.

각 테이블 간에는 관계가 있다. relation이라고 부른다.

요약: table. column. row.

**CREATE**

데이터베이스 안에 데이터는 테이블로 저장한다. 데이터는 컬럼과 로우로 구성된다. 

테이블 생성할때 컬럼을 정해준다. 텍스트는 varchar. 숫자는 number. 날짜는 date 타입으로 컬럼을 만든다.

초급: CREATE 뒤에 이름을 써주고 () 하고 원하는 컬럼을 정의해주기. 데이터 타입과 사이즈 정해줌.

중급: not null은 빈값일수없다는 제한. default는 컬럼에 저장되는 기본값. 교수 테이블에 퇴직여부 컬럼은 Y, N으로 결정될때 default가 Y이면 데이터가 없을때 Y로 저장된다.

고급: pk는 primary key, 중복되면 안되는 데이터가 들어있는 컬럼. comment는 테이블, 컬럼의 상세한 정보. 

ex) 넷플릭스에서 볼수있는 영상 데이터 테이블 생성

컬럼이름 video name. 데이터 타입은 문자열 varchar. 사이즈 50 byte
number type의 byte를 정해줄때 100만뷰까지면 7. 

```sql
CREATE TABLE NETFLIX (
  VIDEO_NAME  VARCHAR2(50),
  CATEGORY  VARCHAR2(30),
  VIEW_CNT NUMBER(7),
  REG_DATE DATE
);
```

실행시키면 table 생성.

```sql
SELECT * FROM NETFLIX;
```
상세한 스키마를 보고싶으면 ctrl 누르고 테이블 클릭하면 자세한 사항이 나온다. 

**ALTER**

alter는 기존에 생성한 테이블을 변경. add, drop, modify. 

넷플릭스 테이블 가져오기. 

```sql
SELECT * FROM NETFLIX;
```

새로운 컬럼 넣어주기.

```sql
ALTER TABLE NETFLIX ADD (CAST_MEMBER VARCHAR2(20));
```

실행 후 전체조회.

ctrl 누르고 table name누르면 cast number 컬럼이 varchar2 20 byte로 생성.

컬럼 변경하기. 컬럼 사이즈 늘리기

```sql
ALTER TABLE NETFLIX MODIFY (CAST_MEMBER VARCHAR2(50));
```

실행 후 ctrl 누르고 클릭하면 50 byte로 변경.

데이터 타입 변경하기. 

```sql
ALTER TABLE NETFLIX MODIFY (CAST_MEMBER NUMBER(2));
```

실행시켜주고 ctrl 클릭 후 cast member 클릭. 

생성한 cast member 컬럼 삭제. 

```sql
ALTER TABLE NETFLIX DROP (CAST_MEMBER);
```

실행시키고 전체조회 해주면 cast member이 삭제됨. 

**DROP/TRUNCATE**

drop table은 테이블 삭제. truncate table은 테이블 초기화.

```sql
CREATE TABLE CODELION (
  COL_1 VARCHAR2(3)
  COL_2 VARCHAR2(3)
);
```

실행시켜준다.

생성된것을 확인하기 위해 select 해준다.

```sql
SELECT * FROM CODELION;
```

데이터를 넣어준다.

```sql
INSERT INTO CODELION VALUES ('AAA', 'BBB');
INSERT INTO CODELION VALUES ('CCC', 'DDD');
```
insert는 커밋해줘야한다. 

```sql
COMMIT;
```
drop table 수행

```sql
DROP TABLE CODELION;
```

다시 테이블 만들기

```sql
CREATE TABLE CODELION (
  COL_1 VARCHAR2(3)
  COL_2 VARCHAR2(3)
);

SELECT * FROM CODELION;

INSERT INTO CODELION VALUES ('AAA', 'BBB');
INSERT INTO CODELION VALUES ('CCC', 'DDD');

COMMIT;

SELECT * FROM CODELION;
```

truncate 해본다.

```sql
TRUNCATE TABLE CODELION;
SELECT * FROM CODELION;
```

확인해보면 데이터만 사라져있다.

## INSERT, UPDATE, DELETE, SELECT, WHERE

**INSERT**

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

**UPDATE**

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

**DELETE**

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

**SELECT**

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

**WHERE**

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
```
```sql
SELECT * FROM NETFLIX WHERE REG_DATE < TO_DATE('20210101', 'YYYYMMDD')
```

이렇게하면 2020년에 등록된 데이터만 조회.

2개 이상의 조건절 주기. 

```sql
SELECT * FROM NETFLIX WHERE CATEGORY = '애니메이션' AND VIEW_CNT < 70;
```

애니메이션이고 2021년 전에 등록된 데이터 조회.

```sql
SELECT * FROM NETFLIX WHERE CATEGORY = '애니메이션' AND REG_DATE < TO_DATE('20210101', 'YYYYMMDD');
```
or 조건도 가능하다.
```sql
SELECT * FROM NETFLIX WHERE CATEGORY = '애니메이션' OR VIEW_CNT < 70;
```

영상의 이름이 '미'로 시작되는 데이터 조회.
```sql
SELECT * FROM NETFLIX WHERE VIDEO_NAME LIKE '미%';
```
끝자리로 조건 주기.
```sql
SELECT * FROM NETFLIX WHERE VIDEO_NAME LIKE '%인';
```
가운데 글자로 조건 주기.
```sql
SELECT * FROM NETFLIX WHERE VIDEO_NAME LIKE '%의%';
```
이렇게 작성하면 의가 앞, 끝에 있어도 된다. '의'가 들어가기만 하면 조회됨. 

view cnt가 60보다 크거나같고 70보다 작거나같은 데이터 조회.
```sql
SELECT * FROM NETFLIX WHERE VIEW_CNT >= 60 AND VIEW_CNT <= 70;

SELECT * FROM NETFLIX WHERE VIEW_CNT BETWEEN 60 AND 70;
```


