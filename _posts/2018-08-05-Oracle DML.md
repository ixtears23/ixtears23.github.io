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

~~~
SELECT T.COLUMN_NAME, T.COLUMN_ID, C.COMMENTS
FROM USER_TAB_COLUMNS T, USER_COL_COMMENTS C
WHERE T.TABLE_NAME = C.TABLE_NAME
AND T.COLUMN_NAME = C.COLUMN_NAME
AND T.TABLE_NAME = '테이블명'
ORDER BY T.COLUMN_ID ASC
~~~


- TIMESTAMP  

~~~
SELECT * FROM AS OF TIMESTAMP(SYSDATE - INTERVAL '1' MINUTE)
FROM 테이블명
~~~


- 오라클 COLUMN 추가 시간  

~~~
SELECT * FROM USER_TAB_COLUMNS A
WHERE A.TABLE_NAME = '테이블명'
~~~

- 오라클 테이블 수정 시간 LAST DDL TIME  

~~~
SELECT * FROM DBA_OBJECTS WHERE OBJECT_NAME = '테이블명'
~~~

- 오라클 SELECT UPDATE / MERGE INTO  

~~~
UPDATE
/*+ bypass_ujvc */
(SELECT A.COL1,
        A.COL2,
        A.COL3,
        A.COL4,
        A.COL5,
        A.COL1_1,
				A.COL2_1,
				A.COL3_1,
				A.COL4_1,
				A.COL5_1
  FROM  테이블명 A
WHERE A.RQST_EMP_NO <> A.INS_ID
  )
SET COL1 = COL1_1
  , COL2 = COL2_1
  , COL3 = COL3_1
  , COL4 = COL4_1
  , COL5 = COL5_1



MERGE INTO 테이블명 A
USING (
      SELECT AA.COL1_1
					 , AA.COL2_1
					 , AA.COL3_1
					 , AA.COL4_1
					 , AA.COL5_1
				FROM 테이블명 AA       
    ) B
ON (A.COL1 = B.COL1_1)
WHEN MATCHED THEN
UPDATE SET A.COL1 = B.COL1_1
         , A.COL2 = B.COL2_1
         , A.COL3 = B.COL3_1
         , A.COL4 = B.COL4_1
         , A.COL5 = B.COL5_1
;
~~~



- 컬럼 코멘트와 데이터 조회  

~~~
SELECT MAX(COL1) AS COL1,
MAX(COL2) AS COL2,
MAX(COL3) AS COL3
FROM (
      SELECT
DECODE(T.COLUMN_NAME,'컬럼명1',C.COMMENTS, '') AS COL1
,DECODE(T.COLUMN_NAME,'컬럼명2',C.COMMENTS, '') AS COL2
,DECODE(T.COLUMN_NAME,'컬럼명3',C.COMMENTS, '') AS COL3
      FROM USER_TAB_COLUMNS T, USER_COL_COMMENTS C
      WHERE T.TABLE_NAME = C.TABLE_NAME
      AND T.COLUMN_NAME = C.COLUMN_NAME
      AND T.TABLE_NAME = '테이블명'
      ORDER BY T.COLUMN_ID ASC)  A
UNION ALL
SELECT COL1
		 , COL2
		 , COL3
  FROM 테이블명
~~~

- 모든 테이블 조회  

~~~
SELECT 'SELECT ''' || TABLE_NAME || ''', '''
      || COLUMN_NAME || ''', COUNT(*) FROM '
      || TABLE_NAME || ' WHERE '
      || COLUMN_NAME || ' LIKE ''%찾고싶은값%'' UNION '
 FROM USER_TAB_COLUMNS
WHERE DATA_TYPE = 'VARCHAR2'
~~~

- FUNCTION, 프로시저 등 스크립트 내용 조회  

~~~
SELECT * FROM USER_SOURCE
WHERE UPPER(TEXT) LIKE '%테이블명%'
~~~
