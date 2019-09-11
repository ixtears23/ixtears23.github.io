---
layout: db-post
title: "SQL Best practices"
date: 2019-09-11
excerpt: ""
tags: [oracle,tunning]
db: true
comments: true
---



일반적으로 Oracle SQL 모범 사례에는 다음 기술이 포함됩니다.  


## 임시 테이블로 복잡한 서브 쿼리 재 작성

> Rewrite complex subqueries with temporary tables

Oracle은 복잡한 SQL 하위 쿼리 (특히 WHERE 절 하위 쿼리, SELECT 절 스칼라 하위 쿼리 및 FROM 절 인라인 뷰가있는 쿼리)를
나누고 정복하는 데 도움이되는 전역 임시 테이블 (GTT) 및 SQL WITH 연산자를 만들었습니다.  

> Oracle created the global temporary table (GTT) and the SQL WITH operator to help divide-and-conquer complex SQL
sub-queries(especially those with with WHERE clause subqueries, SELECT clause scalar subqueries and FROM clause in-line views).  

임시 테이블 (및 WITH 절의 구체화)로 SQL을 실행하면 성능이 크게 향상 될 수 있습니다.  

> Tunning SQL with temporary tables (and materializations in the WITH clause) can result in amazing performance improvements.  

## EXISTS 하위 쿼리 대신 minus 사용

> Use minus instead of EXISTS subqueries  

`NOT IN` 및 `NOT EXISTS` 대신 `minus` 연산자를 사용하면 실행 계획이 더 빨라질 수도 있습니다.  

> Some say that using the munus operator instead of NOT IN and NOT Exists will result in a faster execution plan.  


## SQL 분석 function 사용

> Use SQL analytic functions  

Oracle 분석 함수는 테이블을 한 번 통과하여 여러 집계 (예 : 큐브 별 롤업)를 수행하여 SQL보고에 매우 빠릅니다.  

> The Oracle analytic functions can do multiple aggregations (e.g.rollup by cube)
with a single pass through the tables, making them very fast for reporting SQL.  

## NOT EXISTS 및 NOT EXISTS 하위 쿼리를 외부 조인으로 다시 작성

> Re-write NOT EXISTS and NOT EXISTS subqueries as outer joins  

NOT 쿼리의 많은 경우 (그러나 열이 NULL로 정의 된 경우에만 해당)
IS NULL 테스트를 사용하여 상관되지 않은 하위 쿼리를 외부 조인으로 다시 작성할 수 있습니다.  
이것은 상관되지 않은 하위 쿼리이지만 외부 조인으로 다시 작성할 수 있습니다.  

> In many cases of NOT queries (but ONLY where a column is defined as NULL),
you can re-write the uncorrelated subqueries into outer joins with IS NULL tests.  
Note that this is a non-correlated sub-query, but it could be re-written as an outer join.  


## 열 이름은 그대로 두십시오.

> Leave column names alone  

함수 기반 인덱스 (일명 FBI)가 없으면 인덱스 열을 계산하지 마십시오. 더 나은 방법으로, 일반적인 where 절 술어가 BIF로 변환 할 필요가 없도록 스키마를 다시 디자인하십시오.  

> Never do a calculation on an indexed column unless you have a matching function-based index (a.k.a. FBI).  
Better yet,re-design the schema so that common where clause predicates do not need transformation with a BIF:

## NOT IN 또는 HAVING의 사용을 피하십시오

> Avoid the use of NOT IN or HAVING  

대신 `NOT EXISTS` 서브 쿼리가 더 빨리 실행될 수 있습니다 (적절한 경우).  

> Instead, a NOT EXISTS subquery may run faster (when appropriate).  


## LIKE 를 피하십시오.

> Avoid the LIKE predicate  

적절한 경우 항상 `LIKE`를 `=`으로 대체하십시오.  

> Always replace a 'like' with an equality, when appropriate  


## Data type을 절대 섞지 마십시오

> Never mix data types  

`WHERE` 절 컬럼 술어가 숫자 인 경우 따옴표를 사용하지 마십시오.  
`char` 인덱스 열의 경우 항상 따옴표를 사용하십시오.  

> If a WHERE clause column predicate is numeric, do not to use quotes. For char index columns, always use quotes. There are mixed data type predicates  


## decode 와 case 사용

> Use decode and case  

`decode` 또는 `case` 기능으로 복잡한 집계를 수행하면 테이블을 선택해야하는 횟수를 최소화 할 수 있습니다.  

> Perfoming complex aggregations with the 'decode' or 'case' functions can minimize the number of times a table has to be selected.


[출처:dba-oracle.com](http://www.dba-oracle.com/s_sql_best%20practices.htm)  


---



# Oracle SQL 작성을위한 10 가지 모범 사례  


> 동일한 결과를 반환하는 쿼리를 작성하는 방법은 여러 가지가 있지만 일부 방법은 다른 방법보다 성능이 우수 할 수 있습니다.  

## 1. Oracle 조인 대신 ANSI 조인 사용


`Oracle SQL`에는 두 가지 방법으로 테이블을 조인 할 수 있습니다. 테이블간에 `JOIN` 키워드를 사용하는 `ANSI` 방법에 익숙 할 수 있습니다.  


~~~SQL
SELECT emp.*, dept.*
FROM emp
INNER JOIN dept ON emp.dept_id = dept.id;

SELECT emp.*, dept.*
FROM emp
LEFT JOIN dept ON emp.dept_id = dept.id;
~~~

~~~SQL
SELECT emp.*, dept.*
FROM emp, dept
WHERE emp.dept_id = dept.id;

SELECT emp.*, dept.*
FROM emp, dept
WHERE emp.dept_id = dept.id(+);

SELECT emp.*, dept.*
FROM emp, dept
WHERE emp.dept_id(+) = dept.id;
~~~

조인 쓰기 권장 사항은 Oracle 스타일 ((+) 기호가있는 WHERE 절) 대신 `ANSI` 스타일 (`JOIN` 및 `ON` 키워드)을 사용하는 것입니다.  
Oracle 가입 가이드에서 이 문제에 대해 글을 썼는데 몇 가지 이유가 있습니다.  


- 큰 쿼리에서 `WHERE` 절을 추가하여 테이블을 조인하는 것을 잊어 버리면 불필요한 `cartesian` 조인과 잘못된 결과가 발생합니다.  

> In large queries, it’s easy to forget to add a WHERE clause to join a table, causing unnecessary cartesian joins and incorrect results  

- `WHERE` 절은 테이블을 조인하는 것이 아니라 레코드를 필터링하는 데 사용해야합니다. 미묘한 차이점이지만 쿼리를 이해하기 쉽게 만듭니다.  

- 조인에 사용되는 섹션과 데이터 필터링에 사용되는 섹션을 알 수 있으므로 `ANSI` 조인을 쉽게 읽을 수 있습니다.  


## 2. function 이 있는 WHERE 절을 피하십시오.  

`Oracle SQL` 작업에 대한 또 다른 권장 사항은 함수를 사용하는 `WHERE` 절을 작성하지 않는 것입니다.  
`SQL`에서 `WHERE` 절은 표시 될 행을 필터링하는 데 사용됩니다. 이들은 종종 열이 특정 값과 같은지 확인하는 데 사용됩니다.  

~~~SQL
WHERE status = ‘A’
~~~

함수를 사용한 값과 열을 비교해야 할 수도 있습니다. 예를 들면 다음과 같습니다.  

~~~sql
WHERE UPPER(last_name) = ‘SMITH’
~~~

다른 예는 다음과 같습니다.  

~~~SQL
WHERE ROUND(monthly_salary) > 2000
~~~


`WHERE` 절의 열에 함수를 사용하지 않아야합니다.
함수가 쿼리에 적용되면 열 자체에 생성 된 인덱스 (예:last_name 또는 monthly_salary)가 사용되지 않으므로
쿼리 속도가 크게 느려질 수 있기 때문입니다.  


열에 함수를 사용하지 않으려면 함수없이 `WHERE` 절을 작성하는 방법이 있는지 고려하십시오.
때로는 존재하지만 다른 경우에는 함수를 작성해야합니다.  

`WHERE` 절의 열에 함수가 필요하면 열에 함수 기반 인덱스 작성을 고려하십시오.
이 쿼리에 사용될 수 있는 열에 적용된 함수의 결과로 생성되는 인덱스 유형입니다.  


## 3. 여러개의 UNION 대신 CASE 사용

기준에 따라 다양한 레코드를 찾는 쿼리의 몇 가지 예를 보았습니다.
기준은 단순한 `WHERE` 절 이상이 아니며 여러 유형의 레코드에 따라 조인 및 기타 기준이 다를 수 있습니다.  

이는 종종 `UNION` 또는 `UNION ALL` 키워드를 사용하여 여러 개의 `SELECT` 쿼리가 결합되어 구현됩니다. 예를 들면 다음과 같습니다.  

~~~sql
SELECT id, product_name
FROM product
WHERE status = ‘X’ AND created_date < TO_DATE(‘2017-01-01’, ‘YYYY-MM-DD’)
UNION ALL
SELECT id, product_name
FROM product
WHERE status = ‘A’ AND product_series = ‘WXT’;
~~~

이것은 간단한 예이지만 종종 다른 쿼리에 다른 테이블에 대한 조인 또는 조회가 포함될 수 있습니다.  

이와 같이 쿼리를 구성한다는 것은 테이블을 여러 번 쿼리해야한다는 것을 의미합니다 (각 `SELECT` 쿼리마다 한 번씩). 이는 매우 비효율적입니다.  
더 효율적으로 실행하기 위해 테이블에 인덱스가있을 가능성이 있지만 시도해 볼만한 다른 방법이 있습니다.
`UNION ALL`을 사용하여 별도의 쿼리를 수행하는 대신 단일 `SELECT` 내에 `CASE` 문 안에 논리를 배치하십시오.  

~~~sql
SELECT id, product_name
FROM (
SELECT id, product_name,
CASE
WHEN status = ‘X’ AND created_date <
         TO_DATE(‘2017-01-01’, ‘YYYY-MM-DD’) THEN 1
WHEN status = ‘A’ AND product_series = ‘WXT’ THEN 1
ELSE 0 END AS prodcheck
FROM product
) sub
WHERE prodcheck = 1;
~~~

이 쿼리는 제품 테이블에서 한 번만 실행되며 `UNION ALL`을 사용하는 별도의 `SELECT` 쿼리와 동일한 결과를 표시합니다.


올바른 레코드를 표시하는 논리는 `CASE` 문에 있습니다.
각 기준 세트마다 하나씩 여러 줄이 있으며 일치하는 항목이 있으면 1을 반환합니다.
이 논리는 모두 하위 쿼리 내부에 있으며 외부 쿼리는 해당 `CASE`가 1 인 레코드 만 표시하도록 필터링합니다.  

`CASE` 문을 작성하는 방법에는 몇 가지가 있지만, 기본 쿼리와 CASE 문에 별도의 쿼리가 아닌 몇 가지 기준 만 사용하는 것이 좋습니다.
그러나 `CASE` 쿼리와 함께 실행되지 않는 `UNION` 쿼리와 함께 사용되는 인덱스가있을 수 있으므로 두 버전의 쿼리 모두 성능을 테스트해야합니다.  

## 4. DISTINCT 사용 최소화

`SQL`의 `DISTINCT` 키워드를 사용하면 중복 결과를 제거하여 결과 세트에서 고유 레코드를 리턴 할 수 있습니다.
이것은 간단 해 보이며 유용한 명령입니다. 많은 경우에 `DISTINCT`를 사용하는 것이 좋지만 다른 문제의 증상 일 수 있습니다.
결과 집합에 여러 테이블의 데이터가 표시되는 경우 중복 결과가 나타날 수 있습니다. 내 쿼리에서 이것을 여러 번 보았습니다.  


레코드가 중복되지 않도록 `DISTINCT` 키워드를 추가하고 싶을 수 있습니다.
그러나 `DISTINCT` 키워드를 추가하면 쿼리에서 비싼 작업이 수행되어 속도가 느려질 수 있습니다.
필요한 결과를 제공하지만 다른 곳에서는 문제를 숨기고 있습니다.
불완전한 `JOIN` 또는 테이블의 잘못된 데이터 또는 고려하지 않은 일부 기준에 의한 것일 수 있으며 이로 인해 중복 행이 발생합니다.
쿼리 또는 데이터의 문제를 해결하는 것이 올바른 솔루션입니다.  

## 5. 테이블을 사용하도록 데이터 값 목록 재 설계  


때로는 여러 값을 기준으로 사용하는 쿼리를 작성해야 할 수도 있습니다. 이것은 종종 `WHERE` 절과 `IN` 키워드로 수행됩니다.  

~~~sql
SELECT *
FROM product
WHERE status IN (‘A’, ‘P’, ‘C’, ‘S’);
~~~

이 쿼리는 원하는 결과를 제공 할 수 있습니다. 나중에 상태 값이 변경되거나 비즈니스 규칙이 변경되면이 목록을 조정해야 할 경우 어떻게됩니까?  

이 목록이 검색어로 코딩 된 경우 검색어를 조정해야합니다. 이로 인해 응용 프로그램 코드 및 배포 프로세스가 변경 될 수 있습니다.  

이를 수행하는 다른 방법은 값을 별도의 테이블에 저장하고이 테이블에 조인하는 것입니다.
예를 들어, 값과 범주가 포함 된 status_lookup 테이블이있을 수 있으며 여기서 범주는 필요한 데이터를 정의합니다.  

그러면 쿼리가 다음과 같을 수 있습니다.  

~~~sql
SELECT product.*
FROM product
INNER JOIN status_lookup ON product.status = status_lookup.status
WHERE status_lookup.category = ‘ACTIVE’;
~~~


이렇게하면 비즈니스 규칙이 변경 될 때마다 status_lookup 테이블의 데이터를 업데이트하기 만하면되고 코드를 변경할 필요가 없습니다.
이 권장 사항은 SQL Code Smells 기사에서도 제안되었습니다.  


## 6. UNION 대신 UNION ALL

결과를 결합하는 데 사용되는 `SQL`에는 `UNION`과 `UNION ALL`의 두 가지 유사한 키워드가 있습니다. 결과 집합으로 작업 할 때 '집합 연산자'라고합니다.  

그들 사이에 약간의 차이가 있습니다. `UNION ALL`은 두 결과 집합의 모든 레코드를 표시하고 `UNION`은 중복을 제외한 모든 레코드를 표시합니다.  

명확히하기 위해 `UNION`은 중복을 제거하고 `UNION ALL`은 제거하지 않습니다.  


이는 `Oracle`에서 `UNION`을 사용하여 결과 세트에서 모든 중복 행을 결합한 후 제거 할 때 추가 단계가 수행됨을 의미합니다. `DISTINCT`를 수행하는 것과 같습니다.  

중복을 제거해야하는 경우 `UNION`을 사용하십시오. 그러나 값만 결합하고 중복에 신경 쓰지 않거나 모든 값을 보려면 `UNION ALL`을 사용하십시오.
쿼리에 따라 동일한 결과를 제공하며 중복 제거가 없으므로 성능이 향상됩니다.  

## 7. Table Aliases(별칭) 사용

쿼리를 향상시키는 가장 좋은 방법은 테이블 별칭을 사용하는 것입니다.
테이블 별칭은보다 쉽게 ​​작성하고 작업 할 수 있도록 쿼리의 테이블에 부여 할 수있는 이름입니다.
예를 들어, `product` 및 `status_lookup` 테이블에 대한 이전 쿼리를 사용하면 테이블 별칭이없는 것처럼 보입니다.  

~~~sql
SELECT product.*
FROM product
INNER JOIN status_lookup ON product.status = status_lookup.status
WHERE status_lookup.category = ‘ACTIVE’;
~~~

테이블 이름 뒤에 이름을 지정하여 테이블 별명을 추가 할 수 있습니다.
이 테이블 별명은 일반적으로 짧고 (한 자 또는 몇 자), 일반적으로 전체 테이블 이름의 약어입니다.  

~~~sql
SELECT p.*
FROM product p
INNER JOIN status_lookup s ON p.status = s.status
WHERE s.category = ‘ACTIVE’;
~~~

`product`에 대한 `p`의 테이블 별명과 `status_lookup`에 대한 `s`의 테이블 별명이 조회의 테이블에 포함됩니다.
그런 다음 해당 테이블을 참조 할 때마다 (`SELECT` 절, `JOIN` 또는 `WHERE` 절에서) 테이블 별명을 사용할 수 있습니다. 읽고 쓰기가 더 쉽습니다.  

또한 `p`와 `s`는 전체 테이블 이름의 약어이므로 일부러 선택되었습니다.
이는 특히 `a` 또는 `b`와 같은 일반 문자를 사용하지 않고 더 큰 쿼리를 작업 할 때 사용하는 것이 좋습니다.
설명 별칭을 사용하면 필드가 어떤 테이블에서 나오는지 훨씬 쉽게 알 수 있습니다.  

## 8. 집계 함수에는 HAVING 만 사용

`Oracle SQL`의 `HAVING` 절은 결과 세트에서 레코드를 필터링하는 데 사용됩니다.
`WHERE` 절과 매우 유사합니다. 그러나 `WHERE` 절은 집계 함수가 적용되기 전에 행을 필터링하고 `HAVING` 절은 집계 함수가 적용된 후에 행을 필터링합니다.
집계 함수를 사용하는 경우 모든 것에 `HAVING`을 사용하고 싶을 수 있지만 쿼리에서 다른 기능을 수행합니다.  

예를 들면 다음과 같습니다.  

~~~sql
SELECT status, COUNT(*)
FROM product
WHERE status IS NOT NULL
GROUP BY status
HAVING COUNT(*) > 1;
~~~

상태에 대한 레코드가 둘 이상인 경우 `NULL`이 아닌 각 제품 상태의 수를 찾을 수 있습니다.
`HAVING` 절만 사용하여 쿼리를 작성하면 다음과 같습니다.  


~~~sql
SELECT status, COUNT(*)
FROM product
GROUP BY status
HAVING status IS NOT NULL
AND COUNT(*) > 1;
~~~

이는 데이터에 따라 다른 결과를 제공 할 수 있습니다.
`HAVING` 절을 사용하여 데이터를 제거하기 전에 모든 데이터를 집계해야하므로 성능이 저하 될 수 있습니다.
또한 다른 규칙 집합을 의미합니다.  

집계 함수에는 `HAVING` 만 사용하고 집계 전에 제한하려는 결과에는 `WHERE`를 사용하십시오.  


## 9. INSERT 문에서 항상 열 지정

`Oracle SQL`의 `INSERT` 문에는 데이터를 삽입 할 열을 지정하는 선택적 구성 요소가 있습니다.  

~~~sql
INSERT INTO tablename (col1, col2… col_n)
VALUES (val1, val2… val_n);
~~~

열이있는 `INSERT` 문의 부분은 선택적 부분입니다. 열이없는 `INSERT` 문은 여전히 ​​작동합니다.  

~~~sql
INSERT INTO product VALUES (1, ‘Large Chair’, 120.00);
~~~

그러나 들어가는 좋은 습관은 `INSERT` 문에 열을 지정하는 것입니다. 이것은 몇 가지 이점이 있습니다.
첫째, 오류나 데이터가 잘못된 열로 들어가는 것을 방지 할 수 있습니다. 열을 지정하지 않으면 열이 삽입되는 순서를 보장 할 수 없습니다.
이로 인해 오류가 발생하거나 데이터가 잘못된 열에 값과 함께 삽입 될 수 있습니다.  

또한 어떤 열이 어떤 값을 나타내는 지 명확합니다.
열이없는 문장을 볼 때 값이 무엇인지 추측해야합니다.
열을 추가하면 각 열에 어떤 값이 있는지 정확히 알 수 있습니다.  

`INSERT` 문에 열을 포함 시키십시오.  

## 10. 공백이있는 객체 이름 피하기

권장하는 마지막 모범 사례는 객체 이름에 공백을 사용하지 않는 것입니다.
`SQL` 온라인의 많은 예는 공백을 포함하는 개체 이름 (예 : 테이블)을 지정합니다.
이러한 예제는 대부분 `Microsoft Access` 또는 `SQL Server` 용이며 테이블 이름 주위에 대괄호 나 따옴표를 포함합니다.  

~~~sql
SELECT id, category_name
FROM “Product Category”;
~~~

공백이있는 테이블 이름을 사용하면 쉽게 읽을 수 있습니다.
그러나 몇 가지 문제가 발생할 수 있습니다.
`Oracle`의 테이블 이름은 대문자로 저장되거나 인용 부호가 있으면 입력 할 때 저장됩니다.
즉,이 표를 참조 할 때마다 따옴표를 사용하고 쓰여진대로 지정해야합니다. 귀하와 다른 개발자에게는 불편합니다.  

또 다른 이유는 쿼리에서이 표를 참조하기가 더 어렵 기 때문입니다. 따옴표로 지정해야하며 쿼리가 올바른지 확인하려면 테이블 별칭을 사용해야합니다.  

공백없이 객체 이름을 지정하는 것이 훨씬 좋습니다. 대신 밑줄을 사용할 수 있습니다.  

~~~sql
SELECT id, category_name
FROM product_category;
~~~

`Oracle 12c`부터 객체 이름의 최대 길이가 30 자에서 32,000 자로 증가했습니다.
즉, 테이블 이름을 밝힐 수있는 더 많은 공간이 생깁니다.
그렇다고 지나치게 과도하지 않다는 것이 아니라 공백을 사용하지 않고 만들고있는 것을 나타내는 이름 만 선택하면됩니다.  

### 요약

따라서 `Oracle SQL` 작업을위한 10 가지 모범 사례가 있습니다.
이들 중 일부는 모든 유형의 `SQL`에 적용 가능하지만 대부분은 `Oracle`에 따라 다릅니다.  


[출처:red-gate.com](https://www.red-gate.com/simple-talk/sql/oracle/10-best-practices-for-writing-oracle-sql/)
