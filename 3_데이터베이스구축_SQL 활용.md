## 3과목 - 데이터베이스 구축





1. 논리 데이터베이스 설계
2. 물리 데이터베이스 설계
3. SQL 응용
4. SQL 활용
5. 데이터 전환

---





## 4. SQL 활용

### 1. 프로시저(Procedure)

- 일종의 트랜잭션 언어, 호출을 통해 실행되어 미리 저장해놓은 SQL 작업을 수행함
- 데이터베이스에 저장되어 수행되어, Stored Procedure라고도 불림
  - 어느 프로그램에서나 호출하여 반복 사용 가능함
- 시스템의 일일 마감 작업, 일괄(Batch) 작업에 주로 사용
- 구성도는 데이터 -> 프로시저 -> 결과
  - DECLARE - 프로시저 명칭, 변수, 인수, 데이터 타입 정의
  - BEGIN / END
  - CONTROL - 조건문, 반복문이 삽입
  - SQL
  - EXCEPTION
  - TRANSACTION - 수행된 작업들의 DB 적용 여부를 결정

- 생성
  - CREATE [OR REPLACE] PROCEDURE 프로시저명(파라미터)
    [지역변수 선언]
    BEGIN
    	프로시저 BODY;
    END;
  - OR PLACE는 동일한 프로시저 이름이 이미 존재하는 경우, 기존의 프로시저를 대체함
  - 파라미터는 IN(프로시저에게 값을 전달), OUT(프로시저가 값을 반환), INOUT(둘 다)
  - BODY에는 적어도 하나의 SQL문이 있어야
- 실행
  - EXCUTE or EXEC or CALL 프로시저명;
- 제거
  - DROP PROCEDURE 프로시저명;





### 2. 트리거(Trigger)

- DB 시스템에서 이벤트가 발생할 때마다 관련 작업이 자동으로 수행됨

- 트리거는 DB에 저장되며, 데이터 변경, 무결성 유지, 로그 메시지 출력의 목적으로 사용됨

- 트리거의 구문에는 DCL 사용 불가

- 구성도는 이벤트 -> 트리거 -> 데이터 변경

  - DECLARE
  - EVENT
  - BEGIN / END
  - CONTROL
  - SQL - DML문이 삽입되어 데이터 관리를 위한 조회, 추가, 수정, 삭제 작업을 수행함
  - EXCEPTION

- 생성

  - CREATE [OR REPLACE] TRIGGER 트리거명 [동작시기 옵션] [동작 옵션] ON 테이블명

    REFERENCING [NEW | OLD] AS 테이블명

    FOR EACH ROW

    [WHEN 조건식]

    BEGIN
    	트리거 BODY;
    END;

  - 동작시기 옵션 - AFTER / BEFORE

  - 동작 옵션 - INSERT / DELETE / UPDATE

  - [NEW | OLD] - 트리거가 적용될 테이블의 별칭을 지정함

    - NEW는 추가되거나 수정에 참여할 튜플들의 집합
    - OLD는 수정되거나 삭제 전 대상이 되는 튜플들의 집합

  - FOR EACH ROW는 각 튜플마다 트리거 적용 의미

  - WHEN - 트리거를 적용할 튜플의 조건을 지정함

  - 예제) 튜플의 어떤 조건에서 치환하는 트리거 정의 시

    ```sql
    CREATE TRIGGER 학년정보_tri BEFORE INSERT ON 학생
    REFERENCING NEW AS new_table --새로 추가될 튜플들의 집합 NEW의 별칭을 <new_table>로 명명
    FOR EACH ROW
    WHEN(new_table.학년 IS NULL)
    BEGIN
    	:new_tabble.학년 := '신입생'; --A := B (A에 B를 치환) --NEW or OLD로 지정된 테이블의 이름 앞에는 콜론이 들어감
    END;
    ```

  - <>는 같지 않다의 뜻

- 제거

  - DROP TRIGGER 트리거명;

- 트리거는 데이터가 변경될 때 자동으로 수행되므로, 호출문 존재x





### 3. 사용자 정의 함수

- DML문에서 반환값을 활용하기 위한 용도의
- 일련의 작업을 연속적으로 처리, 처리 결과를 단일값으로 반환하는 절차형 SQL임
- DML문의 호출에 의해 실행됨
- 테이블 조작은 불가, SELECT를 통한 조회만 가능
- 프로시저를 호출하여 사용할 수 없음

| 구분             | 프로시저                  | 사용자 정의 함수 |
| ---------------- | ------------------------- | ---------------- |
| 반환값           | 없음 or 1개 이상 가능     | 1개              |
| 파라미터         | 입출력 가능               | 입력만 가능      |
| 사용 가능 명령문 | DML, DCL                  | SELECT           |
| 호출             | 프로시저, 사용자 정의함수 | 사용자 정의 함수 |
| 사용 방법        | 실행문                    | DML에 포함       |



- 구성도는 데이터 -> 사용자 정의함수 -> 반환
  - 즉프로시저의 구성에서 RETURN만 추가

    - DECLARE
      BEGIN
      	CONTROL

      ​	SQL

      ​	EXCEPTION

      ​	**RETURN**
      END

- 생성
  - CREATE FUNCTION
  - BODY에서 IF 문은 IF (조건) THEN~ELSE~END IF;
- 실행
  - DML에서 속성명이나 값이 놓일 자리를 대체해서 사용됨
  - SELECT **사용자 정의 함수명** FROM 테이블명;
  - INSERT INTO 테이블명(속성명) VALUES (**사용자 정의 함수명**);
  - DELETE FROM 테이블명 WHERE 속성명 = **사용자 정의 함수명**;
  - UPDATE 테이블명 SET 속성명 = **사용자 정의 함수명**;
- 제거
  - DROP FUNCTION **사용자 정의 함수명**;





### 4. DBMS 접속 기술

- DBMS 접속
  - 사용자가 데이터를 사용하기 위해 응용 시스템을 이용하여 DBMS에 접근하는 것
  - 응용 시스템은 사용자로부터 매개변수를 전달받아 - SQL 실행 - DBMS로부터 전달받은 결과를 사용자에게 전달하는 매개체 역할
  - 웹 응용시스템
    - 사용자 - 웹서버 - WAS - DBMS
    - 웹서버 -> WAS : 대용량 데이터 요청시 전달
    - WAS -> DBMS: 트랜잭션 언어로 변환 후 전달
    - 이렇게 받은 데이터는 처음 요청한 웹 서버로 다시 전달되어 사용자에게 도달

- DBMS 접속 기술
  - DBMS에 접근하기 위해 사용하는 API, API의 사용을 편리하게 도와주는 프레임워크
  - JDBC(Java DataBase Connectivity)
    - Java언어, 표준 API, Java SE에 포함, 접속하려는 DBMS에 대한 드라이버가 필요
  - ODBC(Open DataBase Connectivity)
    - 마소, 데이터베이스에 접근 위한 표준 개방형 API, 개발 언어에 관계 없음
    - 드라이버 필요하지만, 접속하려는 DBMS의 인터페이스를 몰라도
      - ODBC 문장으로 SQL 작성하면 해당 DBMS의 인터페이스에 맞게 연결해줌. 즉 DBMS 종류 몰라도 됨
  - MyBatis
    - JDBC코드를 단순화, SQL Mapping 기반 오픈 소스 접속 프레임워크
    - SQL 문장을 분리 -> XML 파일 -> Mapping 통해 SQL 실행
    - SQL을 거의 그대로 사용 가능

- 동적 SQL

  - 사용자가 응용 프로그램을 실행시킨 후 입력란에 SQL을 직접 입력하여 결과를 확인할 수 있는 것

  |           | 정적 SQL                                      | 동적 SQL                                          |
  | --------- | --------------------------------------------- | ------------------------------------------------- |
  | SQL 구성  | 커서를 통한 정적 처리                         | 문자열 변수에 담아 동적 처리                      |
  | 개발 패턴 | 커서의 범위 안에서 반복문을 활용하여 SQL 작성 | NVL 함수 없이 로직을 통해 SQL 작성                |
  | 실행 속도 | 빠름                                          | 느림 (but 유연)                                   |
  | 사전 검사 | 가능                                          | 불가능 (프리컴파일시 구문분석, 접근권한 확인불가) |

  - 커서 - SQL문의 실행 결과로 반환된 복수 개의 튜플에 접근할 수 있도록 해주는 기능
  - NVL 함수 - NVL(A, B) 형태, A가 NULL인 경우 B를 반환하고, 아니면 A 반환
  - 프리컴파일 - 필요한 라이브러리를 불러오거나 코드에 삽입된 SQL문을 DB와 연결하는 작업 수행





### 5. SQL 테스트

- SQL이 작성 의도에 맞게 원하는 기능을 수행하는지 검증

- 단문 SQL 테스트

  - DDL, DML, DCL이 포함된 SQL과 TCL을 테스트함, 직접 실행하여 결과를 확인
  - DESCRIBE, DESC - DDL로 작성된 테이블, 뷰의 속성, 자료형, 옵션을 바로 확인
  - DML로 변경한 데이터는 SELECT로 확인

  - DCL로 설정한 사용자 권한은 SELECT 또는 SHOW로 확인
  - 전체 사용자 목록 확인 - Oracle <dba_users> 테이블, MySQL <user> 테이블 조회

- 절차형 SQL 테스트

  - 디버깅을 통해 기능의 적합성 여부를 검증, 실행을 통해 결과를 확인
  - SHOW ERRORS; - 오류 내용 확인하고 문제를 수정함
  - DB에 변화를 줄 수 있는 SQL문은 주석 처리 뒤 출력하여 확인
    - Oracle
      - DBMS_OUTPUT.ENABLE;
      - DBMS_OUTPUT.PUT_LINE(데이터);
    - MySQL
      - SELECT (데이터);





### 6. ORM(Object-Relational Mappint)

- 객체와 관계형데이터베이스의 데이터를 연결하는 기술
- ORM으로 생성된 가상의 객체지향 DB는 독립적, 재사용, 유지보수 용이
- ORM은 SQL 코드를 직접 입력하지 않고 선언문과 할당 같은 부수적 코드가 생략되기 때문에 직관적, 간단
- ORM 프레임워크
  - Python - Django, SQLAlchemy, Storm
- ORM의 한계
  - 프레임워크가 자동으로 SQL을 생성 - 의도대로 작성되었는지 확인해야
  - 프로젝트가 크고 복잡할수록 ORM 기술 적용 어렵





### 7. 쿼리 성능 최적화

- 성능 측정 도구 APM 사용
- 최적화할 쿼리에 대해 옵티마이저가 수립한 수행 계획을 검토하고, SQL 코드와 인덱스를 재구성함

|             | RBO(규칙 기반)                      | CBO(비용 기반), 실무             |
| ----------- | ----------------------------------- | -------------------------------- |
| 최적화 기준 | 규칙에 의한 우선순위                | 엑세스 비용                      |
| 성능 기준   | 개발자의 SQL 숙련도                 | 옵티마이저의 예측 성능           |
| 특징        | 실행 계획 예측이 쉬움               | 성능 통계치 정보 활용, 예측 복잡 |
| 고려사항    | 개발자의 규칙 이해도, 규칙의 효율성 | 비용 산출 공식의 정확성          |

- 실행 계획은 EXPLAIN 명령어로 확인
- EXISTS - 서브 쿼리의 모든 정보를 확인하는 IN과 달리, 데이터의 존재 여부가 확인되면 종료됨. 속도 빠름
- 단일 인덱스로 조회만 이루어지는 테이블은 IOT(Index-Organized Table)로 구성함