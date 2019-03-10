---
layout: db-post
title: "Oracle DDL"
date: 2017-09-07
excerpt: ""
tags: [db,ddl,oracle]
db: true
comments: true
---



- 테이블 PK 함께 추가  

~~~
CREATE TABLE 테이블명
(
  ID VARCHAR2(20) NOT NULL,
  NAME VARCHAR2(20) NOT NULL,
  USE_YN VARCHAR2(20),
  INS_ID VARCHAR2(20),
  INS_DT DATE,
  CONSTRAINT 테이블명_PK PRIMARY KEY
  (
    ID,
    NAME
  )
  ENABLE
);
~~~

- 인덱스(index) 생성  

~~~
  CREATE INDEX "계정"."INDEX명" ON "계정"."테이블명" ("컬럼")
  PCTFREE 10 INITRANS 2 MAXTRANS 255 COMPUTE STATISTICS
  STORAGE(INITIAL 65536 NEXT 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645
  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)
  TABLESPACE "테이블스페이스"
  ;
~~~



- 테이블 컬럼 추가  

~~~
	ALTER TABLE 테이블명 ADD (컬럼명 자료형)
	ALTER TABLE TEST ADD (RQST_NO VARCHAR(10))
~~~

- 테이블 컬럼 명 변경  

~~~
	ALTER TABLE 테이블명 RENAME COLUMN 컬러명 TO 변경되는컬럼명
~~~


- 테이블 컬럼 크기 수정  

~~~
	ALTER TABLE 테이블명 MODIFY(컬럼명 VARCHAR2(40));
~~~

- 테이블 컬럼 삭제  

~~~
	ALTER TABLE 테이블명 DROP COLUMN 컬럼명;
~~~



- 테이블 복사(데이터 포함)  

~~~
	CREATE TABLE 생성할 테이블명 AS SELECT * FROM 복사할 테이블명(WHERE 절)
~~~

- 테이블 구조만 복사  

~~~
	CREATE TABLE 생성할 테이블명 AS SELECT * FROM 복사할 테이블명 WHERE 1 = 2
~~~

- 오라클 PK 삭제  

~~~
	ALTER TABLE TABLE명 DROP CONSTRAINT PK명
~~~

- 오라클 PK 추가  

~~~
ALTER TABLE TABLE명 ADD CONSTRAINT PK명 PRIMARY KEY(COLUMN 명)
~~~

- 오라클 FK 추가  

~~~
ALTER TABLE 테이블명
ADD CONSTRAINT FK_테이블명
FOREIGN KEY(FK)
REFERENCES 참조테이블명(FK)
~~~
