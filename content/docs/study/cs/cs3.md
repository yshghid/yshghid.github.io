---
date : 2025-01-05
weight: 501
tags: ['2025-01']
categories: ['CS']
bookHidden: true
title: "CS"
---

# sqld 강의 | sql 전문가 정미나

##### 2025-01-05

---

## 데이터베이스란?

데이터는 어딘가에 기록되어 있다. 중요한 데이터는 암호화해서 저장한다.

sql은 데이터를 다루는데 사용되는 언어. 

## 테이블이란?

테이블은 데이터베이스를 구성. 각 테이블은 기능이 갖고 정보를 저장하고 있다.

특정 용도 데이터를 담기 위한 테이블은 entity. 

테이블을 구성하는 속성은 column. attribute라고도 한다. 한사람의 데이터를 의미하는 행은 row. record, tuple.

각 테이블 간에는 관계가 있다. relation이라고 부른다.

요약: table. column. row.

## CREATE

데이터베이스 안에 데이터는 테이블로 저장한다. 데이터는 컬럼과 로우로 구성된다. 

테이블 생성할때 컬럼을 정해준다. 텍스트는 varchar. 숫자는 number. 날짜는 date 타입으로 컬럼을 만든다.

초급: CREATE 뒤에 이름을 써주고 () 하고 원하는 컬럼을 정의해주기. 데이터 타입과 사이즈 정해줌.

중급: not null은 빈값일수없다는 제한. default는 컬럼에 저장되는 기본값. 교수 테이블에 퇴직여부 컬럼은 Y, N으로 결정될때 default가 Y이면 데이터가 없을때 Y로 저장된다.

고급: pk는 primary key, 중복되면 안되는 데이터가 들어있는 컬럼. comment는 테이블, 컬럼의 상세한 정보. 

ex) 넷플릭스에서 볼수있는 영상 데이터 테이블 생성

컬럼이름 video name. 데이터 타입은 문자열 varchar. 사이즈 50 byte
number type의 byte를 정해줄때 100만뷰까지면 7. 

```
CREATE TABLE NETFLIX (
  VIDEO_NAME  VARCHAR2(50),
  CATEGORY  VARCHAR2(30),
  VIEW_CNT NUMBER(7),
  REG_DATE DATE
);
```

실행시키면 table 생성.

```
SELECT * FROM NETFLIX;
```
상세한 스키마를 보고싶으면 ctrl 누르고 테이블 클릭하면 자세한 사항이 나온다. 

## ALTER

alter는 기존에 생성한 테이블을 변경. add, drop, modify. 

넷플릭스 테이블 가져오기. 

```
SELECT * FROM NETFLIX;
```

새로운 컬럼 넣어주기.

```
ALTER TABLE NETFLIX ADD (CAST_MEMBER VARCHAR2(20));
```

실행 후 전체조회.

ctrl 누르고 table name누르면 cast number 컬럼이 varchar2 20 byte로 생성.

컬럼 변경하기. 컬럼 사이즈 늘리기

```
ALTER TABLE NETFLIX MODIFY (CAST_MEMBER VARCHAR2(50));
```

실행 후 ctrl 누르고 클릭하면 50 byte로 변경.

데이터 타입 변경하기. 

```
ALTER TABLE NETFLIX MODIFY (CAST_MEMBER NUMBER(2));
```

실행시켜주고 ctrl 클릭 후 cast member 클릭. 

생성한 cast member 컬럼 삭제. 

```
ALTER TABLE NETFLIX DROP (CAST_MEMBER);
```

실행시키고 전체조회 해주면 cast member이 삭제됨. 

### DROP/TRUNCATE

drop table은 테이블 삭제. truncate table은 테이블 초기화.

```
CREATE TABLE CODELION (
  COL_1 VARCHAR2(3)
  COL_2 VARCHAR2(3)
);
```

실행시켜준다.

생성된것을 확인하기 위해 select 해준다.

```
SELECT * FROM CODELION;
```

데이터를 넣어준다.

```
INSERT INTO CODELION VALUES ('AAA', 'BBB');
INSERT INTO CODELION VALUES ('CCC', 'DDD');
```
insert는 커밋해줘야한다. 

```
COMMIT;
```
drop table 수행

```
DROP TABLE CODELION;
```

다시 테이블 만들기

```
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

```
TRUNCATE TABLE CODELION;
SELECT * FROM CODELION;
```

확인해보면 데이터만 사라져있다.

