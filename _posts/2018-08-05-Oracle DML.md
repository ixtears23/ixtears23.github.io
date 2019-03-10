---
layout: db-post
title: "Oracle DML"
date: 2017-09-07
excerpt: ""
tags: [db,dml,oracle]
db: true
comments: true
---



	- 테이블 컬럼 코멘트 조회
  ~~~oracle
		SELECT T.COLUMN_NAME, T.COLUMN_ID, C.COMMENTS
		FROM USER_TAB_COLUMNS T, USER_COL_COMMENTS C
		WHERE T.TABLE_NAME = C.TABLE_NAME
		AND T.COLUMN_NAME = C.COLUMN_NAME
		AND T.TABLE_NAME = '테이블명'
		ORDER BY T.COLUMN_ID ASC
	~~~


	- TIMESTAMP
  ~~~oracle
		SELECT * FROM AS OF TIMESTAMP(SYSDATE - INTERVAL '1' MINUTE)
		  FROM 테이블명
      ~~~


	- 오라클 COLUMN 추가 시간
  ~~~oracle
		SELECT * FROM USER_TAB_COLUMNS A
		WHERE A.TABLE_NAME = '테이블명'
    ~~~

	- 오라클 테이블 수정 시간 LAST DDL TIME
  ~~~oracle
		SELECT * FROM DBA_OBJECTS WHERE OBJECT_NAME = '테이블명'
    ~~~

	- 오라클 SELECT UPDATE / MERGE INTO
  ~~~oracle
		UPDATE
		/*+ bypass_ujvc */
		(SELECT A.RQST_EMP_NO AS RQST_EMP_NO,
		              A.RQST_DEPT_CD AS RQST_DEPT_CD,
		              A.RQST_GRD_CD AS RQST_GRD_CD,
		              A.RQST_JOB_POST AS RQST_JOB_POST,
		              A.RQST_TEL_NO AS RQST_TEL_NO,
		              A.INS_ID AS INS_ID,
		              FN_GET_DEPT_CD(INS_ID) AS DEPT_CD,
		              FN_GET_GRD_CD(INS_ID) AS GRD_CD,
		              (SELECT OFFC_TEL_NO FROM VI_SYS_USER
		              WHERE USER_ID = A.INS_ID) AS OFFC_TEL_NO,
		              (SELECT JOB_POST FROM VI_SYS_USER
		              WHERE USER_ID = A.INS_ID) AS JOB_POST
		    FROM  테이블명 A
		  WHERE A.RQST_EMP_NO <> A.INS_ID
		    )
		SET RQST_EMP_NO = INS_ID
		     , RQST_DEPT_CD = DEPT_CD
		     , RQST_GRD_CD = GRD_CD
		     , RQST_JOB_POST = JOB_POST
		     , RQST_TEL_NO = OFFC_TEL_NO



		MERGE INTO 테이블명 A
		USING (
		        SELECT AA.RQST_NO, BB.EMP_NO, BB.GRD_CD, BB.DEPT_CD, BB.JOB_POST, CC.OFFC_TEL_NO
		          FROM 테이블명 AA
		          JOIN HRM_BAS_DTL BB ON AA.INS_ID = BB.EMP_NO
		          JOIN VI_SYS_USER CC ON AA.INS_ID = CC.USER_ID
		         WHERE AA.RQST_EMP_NO <> AA.INS_ID
		      ) B
		ON (A.RQST_NO = B.RQST_NO)
		WHEN MATCHED THEN
		  UPDATE SET A.RQST_EMP_NO = B.EMP_NO
		           , A.RQST_DEPT_CD = B.DEPT_CD
		           , A.RQST_GRD_CD = B.GRD_CD
		           , A.RQST_JOB_POST = B.JOB_POST
		           , A.RQST_TEL_NO = B.OFFC_TEL_NO
		;
    ~~~



	- 컬럼 코멘트와 데이터 조회
	예제)
  ~~~oracle
		SELECT MAX(COL1) AS RQST_NO,
		MAX(COL2) AS RQST_EMP_NO,
		MAX(COL3) AS RQST_DEPT_CD
		 FROM (
		        SELECT
		DECODE(T.COLUMN_NAME,'RQST_NO',C.COMMENTS, '') AS COL1
		,DECODE(T.COLUMN_NAME,'RQST_EMP_NO',C.COMMENTS, '') AS COL2
		,DECODE(T.COLUMN_NAME,'RQST_DEPT_CD',C.COMMENTS, '') AS COL3
		        FROM USER_TAB_COLUMNS T, USER_COL_COMMENTS C
		        WHERE T.TABLE_NAME = C.TABLE_NAME
		        AND T.COLUMN_NAME = C.COLUMN_NAME
		        AND T.TABLE_NAME = '테이블명'
		        ORDER BY T.COLUMN_ID ASC)  A
		UNION ALL
		SELECT RQST_NO    ,
		RQST_EMP_NO    ,
		RQST_DEPT_CD
		 FROM 테이블명
		 ~~~

     모든 테이블 조회
     ~~~oracle
     SELECT 'SELECT ''' || TABLE_NAME || ''', '''
            || COLUMN_NAME || ''', COUNT(*) FROM '
            || TABLE_NAME || ' WHERE '
            || COLUMN_NAME || ' LIKE ''%찾고싶은값%'' UNION '
       FROM USER_TAB_COLUMNS
      WHERE DATA_TYPE = 'VARCHAR2'
     ~~~

     FUNCTION, 프로시저 등 스크립트 내용 조회
     ~~~db
     SELECT * FROM USER_SOURCE
     WHERE UPPER(TEXT) LIKE '%TB_ACT_COMM_CD_M%'
     ~~~
