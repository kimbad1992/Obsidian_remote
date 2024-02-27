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

2024년 2월 1일부터 2024년 12월 31일

