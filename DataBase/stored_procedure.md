# 스토어드 프로시저 (저장 프로시저)
어떠한 동작을 **`일괄 처리`** **하기 위한 용도**로 사용된다. => 쿼리문의 집합

형식:
```sql
DELIMITER $$
CREATE PROCEDURE 스토어드_프로시저이름(IN 또는 OUT 파라미터)
BEGIN
    이 부분에 SQL 프로그래밍 코딩
END $$
DELIMITER ;

CALL 스토어드_프로시저이름();
```
- DELIMITER : 실제 코드에서 세미콜론(;)을 사용하기 때문에 사용하는 것이다.
  - 세미콜론을 다른 문자로 바꿔주고 코드가 끝나면 원래대로 세미콜론으로 돌려 놓는다.

## 매개 변수의 사용
- **입력 매개변수** : **`IN`** 으로 사용한다. 호출(CALL)을 통해 값이 전달된다.
```sql
IN 입력_매개변수_이름 데이터형식
```
```sql
CALL 프로시저_이름(전달 값);
```
- **출력 매개변수** : **`OUT`** 으로 사용한다. 값을 실어 변수로 사용한다.
```sql
OUT 출력_매개변수_이름 데이터형식
```
```sql
CALL 프로시저_이름(@변수명);
SELECT @변수명
```

## 스토어드 프로시저의 특징
1. MySQL의 **`성능을 향상`** 시킬 수 있다.
2. **`유지관리`** 가 간편하다.
3. 모듈식 프로그래밍이 가능하다.
4. **`보안을 강화`** 할 수 있다.

# 스토어드 함수
- 사용자가 **직접 만들어서 사용하는 함수**이다.
- 형식
```sql
DELIMITER $$
CREATE FUNCTION 스토어드_함수이름(파라미터)
  RETURNS 반환형식
BEGIN
  이 부분에 프로그래밍 코딩
  RETURN 반환값;
END $$
DELIMITER ;
SELECT 스토어드_함수이름();
```
- **`반환값(Return)이 존재`** 한다는 것이 **스토어드 프로시저와의 차이점**이다.
- **`SELECT로 호출`** 해 사용한다. 그리고 **함수 안에는 SELECT문을 사용할 수 없다.**
- 스토어드 함수를 사용하기 위해서는 "스토어드 함수 생성 권한"을 허용해줘야 한다.
```sql
-- "스토어드 함수 생성 권한"을 허용
SET GLOBAL log_bin_trust_function_creators = 1;
```

## 커서
- **반복 구간**을 설정해 **특정 작업을 행이 끝날 때 까지 수행**한다.
- **스토어드 프로시저 내부에서 사용**할 수 있다.
- 일반 프로그래밍 언어의 파일 처리와 방법이 비슷하다.
### 커서의 처리 순서
1. 커서의 **`선언`** (DECLARE CURSOR)
2. **`반복 조건`** 선언(DECLARE CONTINUE HANDLER) : 행을 읽을 때 더 이상 읽을 행이 없을 경우 실행할 내용을 설정한다.
3. 커서 **`열기`** (OPEN)
4. 커서에서 **`데이터 가져오기`** (FETCH)
5. **`데이터 처리`** : 4번과 5번을 반복한다. 한 행씩 가져와서 행이 없을 때 까지 데이터를 처리하는 것이다.
6. 커서 **`닫기`** (CLOSE)

### 커서 예제 쿼리문
```sql
DELIMITER $$
CREATE PROCEDURE cursorProc()
BEGIN
  DECLARE userHeight INT;
  DECLARE cnt INT DEFAULT 0;
  DECLARE totalHeight INT DEFAULT 0;

  DECLARE endOfRow BOOLEAN DEFAULT FALSE;

  -- 키의 평균을 계산하기 위한 커서 선언
  DECLARE userCursor CURSOR FOR
    SELECT height FROM userTbl;

  -- 행의 끝이면 endOfRow 변수에 TRUE 대입
  DECLARE CONTINUE HANDLER
    FOR NOT FOUND SET endOfRow = TRUE;

  OPEN userCursor;

  cursor_loop: LOOP
    FETCH userCusor INTO userHeight; -- 고객 키 1개를 대입

    IF endOfRow THEN
      LEAVE cursor_loop;
    END IF;

    SET cnt = cnt + 1;
    SET totalHeight = totalHeight + userHeight;
```

## 트리거
**`트리거`** 란 테이블에 삽입, 삭제, 수정 등의 작업이 발생할 때 자동으로 작동하는 개체이다.<br/>
직접 실행시킬 수 없고 트리거가 부착되어 있는 테이블에 이벤트가 발생해야만 실행된다.

### 사용 예제
```sql
DELIMITER //
CREATE TRIGGER testTrg  -- 트리거 이름
  AFTER DELETE  -- 삭제 후에 작동하도록 지정
  ON testTbl  -- 트리거를 부착할 테이블
  FOR EACH ROW  -- 각 행마다 적용시킴
BEGIN
  SET @msg = '가수 그룹이 삭제됨' ; -- 트리거 실행시 작동되는 코드들
END //
DELIMITER ;
-- -----------------------
DELETE FROM testTbl WHERE id = 4;
SELECT @msg; -- 출력결과 '가수 그룹이 삭제됨'
```

### 트리거 종류
**`AFTER 트리거`** 와 **`BEFORE 트리거`** 가 있다.<br/>
**`AFTER 트리거`** : 삽입, 수정, 삭제 등 작업이 일어났을 때 작동하는 트리거<br/>
**`BEFORE 트리거`** : 이벤트가 발생하기 전에 작동하는 트리거

:mag_right: **알아둘 점** <br/>
:one: 트리거는 **`ALTER TRIGGER`** 문을 사용할 수 없다. **`DROP TRIGGER`** 로 삭제 후 **`CREATE TRIGGER`** 로 다시 생성해줘야 한다. <br/>
:two: 트리거는 **`DML`** 문에서 작동한다. **`TRUNCATE는 DDL`** 문이므로 트리거를 작동 시키려면 **DML문의 DELETE**를 써야한다.<br/>

### 트리거가 생성하는 임시 테이블
**`NEW`** : INSERT와 UPDATE 작업 수행 시 **`변경할 새로운 데이터를 저장`** 하는 임시 공간 <br/>
**`OLD`** : DELETE와 UPDATE 작업 수행 시 삭제 또는 변경되기 전의 **`예전 값이 저장`** 되는 임시 공간. <br/>

### 기타
**`다중 트리거`** : 하나의 테이블에 동일한 트리거 여러 개가 부착되어 있는 것 <br/>
**`중첩 트리거`** : **트리거 작동 시 또 다른 트리거가 작동되는 것**
### 참고
> 이것이 MySQL이다 개정판 - 한빛미디어, 우재남 지음 