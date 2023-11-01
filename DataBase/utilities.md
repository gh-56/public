# MySQL 유틸리티 사용법

## Workbench 주요 기능
  - 데이터베이스 연결 기능
  - 인스턴스 관리
  - 위저드를 이용한 MySQL의 동작
  - 통합된 기능의 SQL 편집기
  - 데이터베이스 모델링 기능
  - 포워드/리버스 엔지니어링 기능
  - 데이터베이스 인스턴스 시작/종료
  - 데이터베이스 내보내기/가져오기
  - 데이터베이스 계정 관리

## 알아둘 것
- root 계정은 데이터베이스의 모든 작업을 할 수 있기 때문에 암호가 유출된다면 심각한 문제가 생길 수 있다.
  - **실무에서는 일반 사용자를 생성**하고 그에 적합한 권한을 부여한다.
- SSL (Secure Socket Layer)
  - 보안을 위한 암호규약
  - 서버와 클라이언트가 통신 할 때 **암호화를 통해 비밀을 유지시켜주고 보안을 강화시켜준다.**

## 워크벤치에서의 쿼리 입력 팁
1. Navigator의 Schemas에서 개체를 드래그해 쿼리 창에 끌어 놓으면 글자가 자동완성 된다.
2. 예약어를 대문자 혹은 소문자로 바꾸는 방법
    - **Edit - Format - UPCASE keywords (lowercase Keywords)**
3. 주석 단축키 : 드래그 후 **ctrl + /**
4. 쿼리문을 표준 형태로 재배열하는 단축키 : 드래그 후 **ctrl + b**
    - ```sql
      -- ctrl + b로 재배열
      SELECT 
        *
      FROM
        membertbl;
      ```
5. 행 데이터 개수가 많을 경우엔 출력되는 쿼리 결과가 1000개로 설정되어 있고 이는 변경할 수도 있다.
6. **자동완성 예약어를 대문자로** 설정하는 방법
    - Edit - Preferences - [SQL Editor] - [Query Editor] - Use UPPERCASE keywords on completion 체크

## 사용자 관리
- 권한은 **단편적인 것**을 말한다.
  - SELECT 권한, INSERT 권한 등
- 역할은 **권한의 집합**을 말한다.
  - DBA의 역할 : SELECT 권한 등 모든 권한
- DBA(director) 유저 생성
  - ```sql
      -- 유저이름 : director
      -- %는 모든 컴퓨터를 의미 (특정 ip 주소로 지정하면 보안상 좋음)
      -- 비밀번호 : director
      CREATE USER director@'%' IDENTIFIED BY 'director';

      -- director에게 모든 DB의 TABLE에 대한 모든 권한을 부여
      -- 다른 사람에게도 권한을 부여할 수 있음 (WITH GRANT OPTION)
      GRANT ALL ON *.* TO director@'%' WITH GRANT OPTION;
    ```
- 사장님(ceo) 유저 생성
  - ```sql
      -- 유저이름 : ceo
      -- 비밀번호 : ceo
      CREATE USER ceo@'%' IDENTIFIED BY 'ceo';

      -- 모든 DB의 TABLE에 대한 SELECT 권한만 부여
      GRANT SELECT ON *.* TO ceo@'%';
    ```
- 일반직원(staff) 유저 생성
  - ```sql
      CREATE USER staff@'%' IDENTIFIED BY 'staff';
      -- shopdb의 모든 TABLE에 특정 권한들 부여
      GRANT SELECT, INSERT, UPDATE, DELETE ON shopdb.* TO staff@'%';
      -- employees의 모든 TABLE에 SELECT 권한 부여
      GRANT SELECT ON employees.* TO staff@'%';
    ```

### 참고
> 이것이 MySQL이다 개정판 - 한빛미디어, 우재남 지음