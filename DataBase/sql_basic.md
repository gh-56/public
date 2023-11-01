# SQL 기본

# 1. SELECT 문
## DB 확인
```sql
  SHOW DATABASES;                 -- 모든 데이터베이스 출력
  USE db명;                        -- 데이터베이스 선택
  SHOW TABLES STATUS;             -- 테이블 목록과 상세 정보 출력
  DESC table명;                    -- 테이블 칼럼명 출력
  SELECT 열1, 열2 .. FROM table명;  -- 출력
```
### MySQL에서 한글 입력
- MySQL에서 CHAR, VARCHAR은 모두 UTF-8을 사용
  - UTF-8
    - 영문/숫자/기호 = 1바이트
    - 한글,일본어 등 = 3바이트
  - 하지만 MySQL에서 내부적으로 공간을 할당하므로
  - 결론적으로 CHAR(10)을 하면 한글이든 영문이든 10글자까지 입력할 수 있다.

## 연산자
### AND 연산 
- 관계연산자 (AND)
```sql
SELECT name, height FROM usertbl WHERE height >= 180 AND height <= 183;
```
- BETWEEN AND 연산자
```sql
SELECT name, height FROM usertbl WHERE height BETWEEN 180 AND 183; -- 연속된 값일 때 사용
```
### OR 연산
- 관계연산자 (OR)
```sql
SELECT name, addr FROM usertbl WHERE addr = '경남' OR addr = '전남' OR addr = '경북';
```
- IN() 연산자
```sql
SELECT name, addr FROM usertbl WHERE addr IN('경남', '전남', '경북'); -- 연속적인 값이 아닌 이산적인 값일 때
```

### LIKE 연산
- 성이 '김'씨 이고 그 뒤는 무엇이든(%) 허용
```sql
SELECT name, height FROM usertbl WHERE name LIKE '김%';
```
- 한 글자 매치 ( _ )
```sql
SELECT name, height FROM usertbl WHERE name LIKE '_종신';
```
- %와 _를 조합해서 사용할 수도 있다.
- **%나 _가 검색할 문자열의 제일 앞에 들어가면 성능에 나쁜 영향을 끼칠 수 있다.**
  - 인덱스가 있더라도 인덱스를 사용하지 않고 전체 데이터를 검색하기 때문이다.

### 서브쿼리
- 쿼리문 안의 쿼리문이 들어있는 것이다.
```sql
SELECT name, height FROM usertbl
  WHERE height > (SELECT height FROM usertbl WHERE name = '김경호');
-- 김경호의 키를 직접 입력하지 않아도 직접 입력한 것과 동일한 결과를 내준다.
```
- 만약 서브쿼리의 출력값이 두 개 이상이라면,
1. ANY : 하나만 만족
```sql
SELECT name, height FROM usertbl
WHERE height >= ANY(SELECT height FROM usertbl WHERE addr = '경남');
-- 서브쿼리의 여러 출력 결과 중 하나만 만족해도 된다.
-- ANY(서브쿼리) = IN(서브쿼리) 이다.
SELECT name, height FROM usertbl
WHERE height >= IN(SELECT height FROM usertbl WHERE addr = '경남');
```
2. ALL : 모두 만족
```sql
SELECT name, height FROM usertbl
WHERE height >= ALL(SELECT height FROM usertbl WHERE addr = '경남');
-- 서브쿼리의 여러 출력 결과 모두 만족해야 된다.
```

### ORDER BY : 순서대로 출력
오름차순 : ASC. 디폴트값  
내림차순 : DESC  
사용방법 : ORDER BY 필드명 정렬기준  
성능을 떨어뜨릴 소지가 있어 꼭 필요한 경우 아니면 사용 하지 않기

### DISTINCT : 중복 제거
사용방법
```sql
SELECT DISTINCT 필드명 FROM 테이블명;
```

### LIMIT : 출력 개수 제한
사용방법
```sql
SELECT 필드명 FROM 테이블명 ORDER BY 정렬 LIMIT 표시할 개수 N개
```
```sql
LIMIT 0, 5 -- 0번째부터 5개. 0부터 시작함
```

- 악성 쿼리문이란?
  - 서버의 처리량을 많이 사용해서 서버 성능을 나쁘게 하는 쿼리문.

## 테이블을 복사하는 방법
값만 복사하는 방법 (제약조건 복사 X)
```sql
CREATE TABLE 새로운테이블(SELECT 복사할 열 FROM 기존테이블);
```

## 집계 함수
| 함수명 | 설명 |
| --- | --- |
|AVG()|평균|
|MIN(), MAX() |최소, 최대|
|COUNT()|행의 개수|
|COUNT(DISTINCT)|중복 제거 행 개수|
|STDEV()|표준편차|
|VAR_SAMP()|분산| 
- 집계 함수는 WHERE 절에 사용할 수 없다. (HAVING절 이용)

예시 코드) 가장 큰 키와 작은 키 출력하기
```sql
SELECT name, height FROM usertbl WHERE height IN ((SELECT MAX(height) FROM usertbl) , (SELECT MIN(height) FROM usertbl));
-- 출력결과
-- 조용필 166
-- 성시경 186
```
## GROUP BY절
- 그룹으로 묶어주는 역할을 한다.

사용 방법
```sql
SELECT 출력할 열 FROM 출력할 테이블 GROUP BY 그룹화할 열
```
사용 예시
```sql
SELECT userID, sum(amount) FROM usertbl GROUP BY userID;
```

## HAVING 절
- 집계 함수에 대한 조건절
- **꼭 GROUP BY절 이후에 나와야 한다.**
```sql
SELECT userID, sum(price*amount) 
  FROM usertbl 
  GROUP BY userID 
  HAVING sum(price*amount) > 1000;
```

## ROLLUP : 총합과 중간합계
```sql
SELECT groupName, SUM(price*amount)
  FROM buytbl
  GROUP BY groupName
  WITH ROLLUP;
-- 출력 결과
-- 서적의 소합계, 의류의 소합계, 전자의 소합계 등이 출력되다 마지막 행에 총합계가 출력된 형태
```
## SQL의 분류
- DML (Data Manipulation Language)
  - 데이터 조작어
  - 데이터를 선택/삽입/수정/삭제하는 역할을 한다.
    - SELECT, INSERT, UPDATE, DELETE 
  - 사용 대상은 테이블의 행이다.
  - 트랜잭션이 발생한다. (INSERT, UPDATE, DELETE가 해당되고 SELECT는 별도로 생각)
    - 트랜잭션: 실제 테이블에 완전히 적용하지 않고, 임시로 적용시키는 것이다. 그래야 실수가 있더라도 임시로 적용시킨 것을 취소시킬 수 있기 때문이다.
    - 여러 DML 구문이 하나의 트랜잭션이 된다.
- DDL (Data Definition Language)
  - 데이터 정의어
  - DB 개체를 생성/삭제/변경하는 역할을 한다.
    - CREATE, DROP, ALTER
  - 트랜잭션을 발생시키지 않는다.
    - DDL문 실행 즉시 MySQL에 적용된다.
- DCL (Data Control Language)
  - 데이터 조작어
  - 사용자에게 어떤 권한을 부여하거나 빼앗는 역할을 한다.
  - GRANT, REVOKE, DENY
# 2. INSERT 문
- 테이블에 데이터를 삽입하는 명령어이다.
- 사용 방법
```sql
INSERT INTO 테이블(열1,열2...) VALUES(값1,값2...);
```
## AUTO_INCREMENT : 자동으로 증가
- AUTO_INCREMENT로 지정되어 있다면 INSERT에서는 해당 열이 없다고 생각하고 입력하면 된다.
- 자동으로 1부터 증가한다.
- PRIMARY KEY 또는 UNIQUE로 지정해줘야한다.
- INSERT 문에서 NULL값을 지정하면 자동으로 값이 입력된다.
- 숫자 증가 확인 코드
  ```sql
  SELECT LAST_INSESRT_ID()
  ```
- 입력값 변경
  ```sql
  ALTER TABLE 테이블명 AUTO_INCREMENT=100;
  ```
- 증가값 지정
  ```sql
  SET @@auto_increment_increment=3; -- 3씩 증가하도록 변경
  ```
- 한꺼번에 INSERT 하는 방법
```sql
INSERT INTO 테이블명 VALUES(값1-1, 값1-2), (값2-1,값2-2), (...);
```
### INSERT INTO ... SELECT 구문
```sql
INSERT INTO 테이블명 (필드명1, 필드명2...) SELECT 문;
```
# UPDATE 문
- 기존에 입력되어 있는 값을 변경할 때 사용한다.
```sql
UPDATE 테이블명
SET 열1=값1, 열2=값2
WHERE 조건;
```

# DELETE 문
- 행 단위 삭제
```sql
DELETE FROM 테이블명 WHERE 조건
```
- 데이터 삭제 방법 3가지
1. DELETE
```sql
DELETE FROM 테이블명;
-- 트랜잭션 로그를 기록하는 작업 때문에 시간이 오래 걸린다.
-- 테이블의 구조는 남겨둔다.
```
2. DROP
```sql
DROP TABLE 테이블명;
-- 테이블 자체를 삭제한다.
```
3. TRUNCATE
```sql
TRUNCATE TABLE 테이블명;
-- 트랜잭션 로그를 기록하지 않아 시간이 빨리 걸린다.
-- 테이블의 구조는 남겨둔다.
```

# 조건부 데이터 입력, 변경
조건부 입력 (IGNORE)
```sql
INSERT IGNORE INTO 테이블명 VALUES(...);
-- PK 중복 시 무시하고(오류 발생 안하고) 넘어간다.
```
조건부 변경 (ON DUPLICATE KEY UPDATE)
```sql
INSERT INTO 테이블명 VALUES ('BBK', '비비코','미국')
ON DUPLICATE KEY UPDATE name='비비코', addr='일본';
-- PK 중복 시 UPDATE 내용 수행
-- PK 중복 아닐 시 INSERT 내용 수행
```

# WITH절과 CTE (Common Table Expression)
- WITH절은 CTE를 표현하기 위한 구문이다.
  - MySQL 8.0 이후부터 사용할 수 있다.
- CTE
  - 기존의 뷰, 파생 테이블, 임시 테이블 등으로 사용되던 것을 대신할 수 있다.
  - 더 간결한 식으로 보여지는 장점이 있다.
  - 비재귀적, 재귀적 CTE로 총 두 가지가 있다.
    - 주로 사용되는 것은 비재귀적 CTE이다.

## 비재귀적 CTE
- 복잡한 쿼리문장을 단순화 시킨다.
```sql
WITH CTE_테이블명(필드명)
AS
(
  쿼리문
)
SELECT 필드명 FROM CTE_테이블명;
```
예시
```sql
WITH cte_usertbl(addr, maxHeight)
AS
(SELECT addr, MAX(height) FROM usertbl GROUP BY addr)
SELECT AVG(maxHeight * 1.0) FROM cte_usertbl;
```

### 참고
> 이것이 MySQL이다 개정판 - 한빛미디어, 우재남 지음