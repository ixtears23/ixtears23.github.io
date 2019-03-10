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
		예)
    ~~~
		CREATE TABLE HRM_IMSL_BASE_MNGM
		(
		  DIV1_CD VARCHAR2(20) NOT NULL,
		  DIV2_CD VARCHAR2(20) NOT NULL,
		  DIV3_CD VARCHAR2(20) NOT NULL,
		  VAL1 VARCHAR2(20),
		  VAL2 VARCHAR2(20),  
		  USE_YN VARCHAR2(20),
		  INS_ID VARCHAR2(20),
		  INS_DT DATE,
		  UPT_ID VARCHAR2(20),
		  UPT_DT DATE
		, CONSTRAINT HRM_IMSL_BASE_MNGM_PK PRIMARY KEY
		  (
		    DIV1_CD,
		    DIV2_CD,
			DIV3_CD
		  )
		  ENABLE
		);
    ~~~

	- 인덱스(index) 생성
		예)
    ~~~
		  CREATE INDEX "IBS"."IDX_HRM_TRP_MST_01" ON "IBS"."HRM_TRP_MST" ("LCTR_RQST_NO")
		  PCTFREE 10 INITRANS 2 MAXTRANS 255 COMPUTE STATISTICS
		  STORAGE(INITIAL 65536 NEXT 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645
		  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 BUFFER_POOL DEFAULT FLASH_CACHE DEFAULT CELL_FLASH_CACHE DEFAULT)
		  TABLESPACE "TS_IBS_IDX"
		  ;
		~~~



	- 테이블 컬럼 추가
  ~~~
		ALTER TABLE 테이블명 ADD (컬럼명 자료형)
		ALTER TABLE HRM_EXPT_PRQ ADD (ADVC_RQST VARCHAR(10))
  ~~~

	- 테이블 컬럼 명 변경
  ~~~
		ALTER TABLE HRM_OUTS_PRUS_RQST RENAME COLUMN OUTS_HPWR_NO TO OSDR_NO
  ~~~


	- 테이블 컬럼 크기 수정
  ~~~
		ALTER TABLE USERS MODIFY(NAME2 VARCHAR2(40));   -- 컬럼 수정할시 크기확인--
  ~~~

	- 테이블 컬럼 삭제
  ~~~
		ALTER TABLE USERS DROP COLUMN NAME3;
  ~~~





	- 오라클 테이블 복사
		테이블 복사(데이터 포함)
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
		ALTER TABLE TABLE명 ADD CONSTRAINT PK명 PRIMARY KEY(COLUMN 명 )
	~~~

	- 오라클 FK 추가
  ~~~
		ALTER TABLE 테이블명
		ADD CONSTRAINT FK_테이블명
		FOREIGN KEY(FK)
		REFERENCES 참조테이블명(FK)
  ~~~
