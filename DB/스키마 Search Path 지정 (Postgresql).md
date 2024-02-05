
```sql
-- 현재 계정이 접속하는 스키마
SHOW SEARCH_PATH;

-- 해당 유저의 탐색 스키마를 변경
ALTER USER `유저명` SET SEARCH_PATH = `DB명,스키마명`;
```
