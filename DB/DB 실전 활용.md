## 인덱스

  

2. 실전 SQL 활용

1) INDEX

1. B-Tree 인덱스  
    1) B-Tree 구조

![](https://lh7-us.googleusercontent.com/hoi4meVIALR2HIlzLXB_eb0oCJmwAhP3Ppp-f4I4hHvqFi9oYO6fnUQGUkpdI300_1K0xOw9OUXm-wUDclo2LzTaf_YQ9H7ju7N7_SyKpwCVGWYpxKvOTtn76-RgwuDTi4cOcnOrxv72Gg1jcguSZA=s2048)  

     2) Index 구조  
        - CREATE INDEX IDX_TB_MYBB_INTG_MBR_01 ON TB_MYBB_INTG_MBR (MBR_NAME ASC)

![[Pasted image 20240227140340.png]]

## 인덱스 상태 검사

```sql
-- Oracle
SELECT OWNER, INDEX_NAME, TABLE_NAME
FROM DBA_INDEXES 
WHERE STATUS = 'UNUSABLE'

-- 인덱스 리빌드
ALTER INDEX [인덱스명] REBUILD;

--- MSSQL
DBCC SHOWCONTIG(테이블명, 인덱스명)

-- 인덱스 리빌드
ALTER INDEX [인덱스명] ON [테이블명] REBUILD;
```


## Subquery

1. 위치에 따라 사용되는 Subquery
	1) SELECT 절
		SELECT절의 행의 수만큼 Subquery를 수행하기 때문에 사용에 주의해야 한다.
	2) FROM 절
		FROM절에 사용하는 Subquery는 인라인 뷰(Inline View)라고 하며, View처럼 결과가 동적으로 생성된 임시적 테이블로서 자유롭게 참조가 가능하지만 Memory에 올라가 인덱스를 사용할 수 없다.
	3) WHERE 절

2. WITH 활용
	1) Inline View
		FROM절에 사용하는 Subquery(Inline View)와 동일하고, 사용하기 편하다.
		호출 할때마다 WITH절 안에 있는 쿼리를 수행한다. (Oracle Materialize 힌트 사용 시 예외적으로 한번만 수행)
		조인 시 인덱스를 사용할 수 없다.
		여러 번 호출되지 않도록 사용하는게 중요하다.
	1) 재귀 쿼리 (Recursive 쿼리)
```SQL
WITH MENU_CTE(MENU_ID, LV, MENU_NM, MENU_PATH) AS (
  SELECT MENU_ID, 1, CONVERT(VARCHAR(2000), MENU_NM), CONVERT(VARCHAR(2000), '/' + MENU_ID)
  FROM TB_MENU
  WHERE PARENT_MENU_ID IS NULL
  UNION ALL
  SELECT A.MENU_ID, B.LV + 1, CONVERT(VARCHAR(2000), REPLICATE(' ', (3*B.LV)) + A.MENU_NM), CONVERT(VARCHAR(2000), B.MENU_PATH + '/' + A.MENU_ID)
  FROM TB_MENU A
       INNER JOIN MENU_CTE B
             ON A.PARENT_MENU_ID = B.MENU_ID
)
SELECT MENU_ID, LV, MENU_NM, MENU_PATH
  FROM MENU_CTE
 ORDER BY MENU_PATH;
```


### 예시

특정 날짜부터 ~까지 재귀 쿼리로 날짜 값 구하기
```sql
WITH DATE_CTE AS (  
    SELECT CAST('2024-02-01' AS DATE) AS STARTDATE  
    UNION ALL  
    SELECT DATEADD(DAY, 1, STARTDATE)  
    FROM DATE_CTE  
    WHERE STARTDATE < '2024-12-31'  
)  
SELECT STARTDATE  
FROM DATE_CTE  
OPTION (MAXRECURSION 0); -- MSSQL 재귀 최대 횟수 지정
```

## Window Function

구조

`ANALYTIC_function ([argument_list]) OVER_clause`

- OVER_clause
```
OVER (
	[<PARTITION BY clause>]
	[<ORDER BY clause>]
	[ROW or RANGE clause>]
)
```

- PARTITION BY clause
	`PARTITION BY value_expression, ... [n]`
- ORDER BY clause
```
ORDER BY order_by_expression
[COLLATE collation_name]
[ASC | DESC]
[, ...n]
```
- ROW or RANGE clause
`{ROWS | RANGE} <window frame extent>`
- window frame between
`BETWEEN <window frame bound> AND <window frame bound>`

### LAG
- `LAG (scalar_expression [, offset][, default])`
- 지정된 파티션 (OVER 안의 PARTITION BY) 있는 현재 ROW (offset) 기준으로 N(기본 1)번째 앞에 있는 Row의 value_expression 값을 반환한다.

```sql
WITH BASE AS (  
    SELECT BRAND_CD, DATEPART(MONTH, ORDER_DATE) AS ORDER_MONTH, SUM(AMOUNT) AS AMOUNT  
    FROM TB_MYBB_PURCHASE  
    WHERE ORDER_DATE >= '2023-01-01'  
      AND ORDER_DATE <  '2024-01-01'  
    GROUP BY BRAND_CD, DATEPART(MONTH, ORDER_DATE)  
)  
SELECT A.BRAND_CD, B.BRAND_NAME, A.ORDER_MONTH, A.AMOUNT  
    ,LAG(A.AMOUNT) OVER(PARTITION BY A.BRAND_CD ORDER BY A.ORDER_MONTH) AS LAG_AMOUNT -- 이전 ROW의 값  
    ,SUM(A.AMOUNT) OVER(PARTITION BY A.BRAND_CD ORDER BY A.ORDER_MONTH) AS ACC_AMOUNT -- 누적 합  
    ,AVG(A.AMOUNT) OVER(PARTITION BY A.BRAND_CD) AS BRAND_AVG -- 평균  
    ,MAX(A.AMOUNT) OVER(PARTITION BY A.ORDER_MONTH) AS MONTH_MAX -- 월별 최대 매출액  
FROM BASE A  
INNER JOIN TB_MYBB_BRAND_INFO B ON A.BRAND_CD = B.BRAND_CD  
ORDER BY A.BRAND_CD, A.ORDER_MONTH
```
1개의 기준 결과 내에서 집계 처리를 전부 할 수 있는 장점

### 번호 지정 함수
- 순서(OVER 안에 ORDER BY)가 지정된 파티션 (OVER 안에 PARTITION BY) 내에서 순위를 반환한다.

```sql
WITH BASE AS (
  SELECT ORDER_NO, BRAND_CD, MARS_ID, AMOUNT
	   , RANK() OVER(PARTITION BY BRAND_CD ORDER BY AMOUNT DESC) AS B_RANK
	   , DENSE_RANK() OVER(PARTITION BY BRAND_CD ORDER BY AMOUNT DESC) AS B_DENSE_RANK
	   , ROW_NUMBER() OVER(PARTITION BY BRAND_CD ORDER BY AMOUNT DESC) AS B_ROW_NUMBER
	FROM TB_MYBB_PURCHASE
   WHERE BRAND_CD IN ('07', '15')
	 AND ORDER_DATE >= '2023-12-31'
	 AND ORDER_DATE <  '2024-01-01'
)
SELECT ORDER_NO, BRAND_CD, MARS_ID, AMOUNT, B_RANK, B_DENSE_RANK, B_ROW_NUMBER
  FROM BASE
 WHERE B_ROW_NUMBER <= 5
 ORDER BY BRAND_CD, B_ROW_NUMBER
```

### 집계 함수

....

---


```sql
-- Hash Join  
SELECT A.INTG_MBR_ID, B.BRAND_CD, B.MARS_ID, C.BRAND_CD, C.ORDER_DATE, C.AMOUNT  
FROM TB_MYBB_INTG_MBR A  
         INNER JOIN TB_MYBB_BRAND_MBR B  
                    ON A.INTG_MBR_ID =  B.INTG_MBR_ID  
         INNER JOIN TB_MYBB_PURCHASE C  
                    ON B.MARS_ID = C.MARS_ID  
WHERE A.MBR_NAME = '최이로'

-- C.MARS_ID에 Index를 걸면 Nested Loop Join으로 변경됨
-- NL Join의 경우 Cost도 300 -> 0.5로 줄어든다
-- 인덱스는 위와 같이 보통은 FK에 걸어주면 좋다
```
-- 

## 파티션

### 파티션 테이블

특정 기준으로 테이블을 나눈다

1) 장점
	-  파티션 별 독립적으로 관리되어 백업, 삭제, 복원이 용이하다.
	- 데이터 접근 시 범위를 줄여 쿼리 성능이 향상된다.
2) 단점
	- 파티션 키/값 변경에 대한 관리가 필요하다.
	- 조건 절에 파티션 기준이 되는 컬럼이 없으면 속도가 느려진다.
3) 종류


- Range Partition : 일, 월, 년 등과 같이 범위로 구분되는 Partition

```sql
[ORACLE]  
create table order_new (
                        order_id number primary key,
                        order_date date,
                        customer_id number,
                        order_amount number,
                        order_store varchar(2)
) partition by range (order_date)
(partition order_part1 values less than (to_date('2023-01-01','yyyy-mm-dd')),
 partition order_part2 values less than (to_date('2023-07-01','yyyy-mm-dd')),
 partition order_part3 values less than (to_date('2024-01-01','yyyy-mm-dd'))
);
```

- Hash Partition : Hash 함수를 사용하여 분배하는 Partition

```sql
[ORACLE]  
create table order_new (
                        order_id number primary key,
                        order_date date,
                        customer_id number,
                        order_amount number,
                        order_store varchar(2)
) partition by hash (order_date)
(partition order_part1,
 partition order_part2,
 partition order_part3
);
```

- List Partition : 단일 컬럼 값에 의해 구분되는 Partition

```sql
[ORACLE]  
create table order_new (
                        order_id number primary key,
                        order_date date,
                        customer_id number,
                        order_amount number,
                        order_store varchar(2)
) partition by list (order_store)
(partition order_part1 values ('01', '04', '07'),
 partition order_part2 values ('02', '05', '08'),
 partition order_part3 values ('03', '06', '09')
);
```