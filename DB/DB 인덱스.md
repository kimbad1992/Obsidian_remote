## Ⅰ. SQL 활용

  

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
