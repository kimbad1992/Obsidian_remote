### 1. CTE 남발 금지
   - INDEX 문제로 속도에 영향을 준다
   - VIEW 테이블과 같은 현상
      - 이미 조건이 걸린 `SELECT`문을 다시 조회하는 것
      
### 2. UNIQUE, PRIMARY KEY의 차이
   - `NOT NULL`, `NULLABLE`의 차이가 있음
   - `Schema - Unique Index - Primary Key` 생성 순서가 정석	
### 3. 인덱스가 안먹는 경우

| A | B | C |
| ---- | ---- | ---- |
| 1 | 가나다 | 2022-01-01 |
  라는 형태의 데이터가 존재할 때, 인덱스를 생성하면
  
| A | B |
| ---- | ---- |
| 1가나다2022-01-01 | 주소값 |
  1개의 Row의 Data Set과 그 Data Set의 주소값을 가지는 주소값이 존재
  (상기 설명은 실제와는 다르지만 개념적인 접근)
  
  
   - 쿼리에서 형 변환시(SUBSTRING, 데이터 타입 변환, 앞뒤 % LIKE 등)
      - `예)SELECT A, B, C FROM 테이블 
      WHERE SUBSTR(B, 1, 2) = '가나'`
      - `예)SELECT A, B, C FROM 테이블 
      WHERE A = '1'`
      1이 문자형이므로 타지 않음(형변환)
      - `예)SELECT A, B, C FROM 테이블 
      WHERE C = '2022-01-01'`
      날짜를 문자형으로 변환하므로 타지 않음
      - `예)SELECT A, B, C FROM 테이블 WHERE B LIKE '가나%'`
      `예)SELECT A, B, C FROM 테이블 WHERE B LIKE '%가나'`
      인덱스를 탐
      - `예)SELECT A, B, C FROM 테이블 WHERE B LIKE '%가나%'`
      인덱스를 타지 않음
    - 부정형 쿼리 수정(
      - `예) SELECT A, B, C FROM 테이블 WHERE IN ('가나', '나다')`
      이 경우 인덱스를 타지만,
      - `예) SELECT A, B, C FROM 테이블 WHERE NOT IN ('가나', '나다')`
      이 경우는 타지 않는다.
      모든 Row와 비교하는 것과 개념적으로 비슷하다.
      - `NOT EXISTS`로 대체
      `예) SELECT A, B, C FROM 테이블 WHERE NOT EXISTS (SELECT 1 FROM 테이블 WHERE B IN ('가나','나다')
      )`
      서브 쿼리에서 인덱스를 타지만, 메인 쿼리 WHERE절에서는 타지 않는다. 다만 서브쿼리에서의 조회 결과가 빨라지므로 속도가 빠르다.
      - `NOT LIKE`는 사용하지 말 것. `NOT IN`보다도 속도가 느리다.
### 4. 인덱스가 많아지면 그만큼 `I/O`가 많이 발생한다.
      
   - 신규 Row `INSERT` 시 테이블, 각각의 인덱스에도 동시에 `INSERT`가 발생하므로 `INSERT` `DELETE` `UPDATE` 등의 속도가 저하 된다.
      `SELECT` 위주의 테이블일 경우 인덱스가 많아도 상관 없지만, `Batch`성의 테이블(`I/O`가 잦게 일어나는 테이블)은 인덱스가 많으면  성능상 손해
     
### 5. 데이터가 적을 경우는 상관 없지만 100만 Row 이상 넘어가면 쿼리의 성능을 고민해보자.
### 6. Unique Scan, Range Scan
### 7. 실행계획을 통한 JOIN방식 적용
![오라클 실행계획의 예시](https://velog.velcdn.com/images/kimbad1992/post/503f8e0c-82fa-45dd-b3f8-50da36ebe8fa/image.png)

   - NL Join(Nested Loop Join)
      - 선행적 특성을 갖는데, 먼저 Access 되는 테이블의 처리범위에 의해 처리량이 결정
      - 2중 for문과 개념상 비슷하지만, Target Table 조회할 때 인덱스를 타므로 빠름
   - Sort Merge Join
      - 연결 전 사전 배치를 정렬(Sort) 한 후 이들을 서로 연결(Merge) 하는 방식
      - 정렬을 해야한다는 단점을 제외하면 Random Access가 아닌 정렬된 집합을 스캔해 가면서 Merge를 하기 때문에 연결 속도가 매우 빠르다
      - 정렬 작업이 속도를 좌우
      - `SELECT * FROM 테이블 WHERE .... ORDER BY A, B`
      `SELECT * FROM ( SELECT * FROM 테이블 WHERE ... LIMIT n) ORDER BY A, B`는 속도가 다르다.
  
   - Hash Join
      - 데이터를 Key, Value로 전부 Hash Cluster(개념 상 메모리)에 올린 후 대조
      - 리소스를 많이 소모하므로 다른 기능들의 성능이 떨어짐
      - Data Size가 큰 것들끼리 Join해서 결과를 얻어야 할 때
      (1000만 Row 테이블 A, 2000만 Row 테이블 B가 조인 하는 등의 경우)
      - Data Size가 크지 않은데 실행 계획 상 Hash Join이 걸린 경우 NL Join으로의 튜닝을 고려해봐야 함
   - 옵티마이저 (RBO, CBO)
      - `Rull-Base Optimizer`
      동작에 대한 힌트(Rule)로 동작, 근래에는 거의 사용되지 않음
      - `Cost-Base Optimizer`
      현재 가용 가능한 자원으로 최적의 비용을 판단해서 동작
### 8. `UPDATE` 구문 시 `JOIN`이 필요한 구분 적용
   - `MERGE INTO` 활용
      - 오라클에서는 다른 테이블과 조인해서 `UPDATE`를 써야할 경우 `MERGE INTO`를 사용할것을 권장
      - 안좋은 예
      `UPDATE 테이블1 A SET A.a = (SELECT B.D FROM 테이블2 B.....)`
      대신 `MERGE INTO`를 사용
### 9. 파티션 개념과 종류 이해
   - 만약 수억, 수백억, 수천억 단위의 Data를 가지는 테이블A가 있다면? 조회할 때 시간 소모가 매우 크고, 쿼리 튜닝에도 한계가 있다.
   ('yyyyMMdd'의 값을 가지는 테이블이라고 가정 했을 때)
   - `SELECT * FROM 테이블A WHERE A = '20220101'`
      라는 쿼리가 실행 될 때 수백억, 수천억건의 데이터를 검색할 시 속도가 느려지므로, `19000101~19911231`까지의 데이터를 가지는 테이블A-1, 테이블A-2... 등의 물리 테이블로 나눈 후 하나의 논리 테이블로 만든다. 
(파티션 개념)

### 10. 서브쿼리(Nested, Inline View, Scalar)
### 11. `UPDATE`, `DELETE` 사용 시 항상 같은 조건으로 SELECT를 먼저 해볼 것