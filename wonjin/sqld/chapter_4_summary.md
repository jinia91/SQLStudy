# 관리 구문
## DML
### INSERT 
> INSERT INTO TABLE [(COL1, COL2....)] VALUES (DATA1,DATA2 ...);

OR

> INSERT INTO TABLE VALUES (DATA1, DATE2 ...);

#### 서브 쿼리를 사용해 다중 행 INSERT

> INSERT INTO TABLE [(COL1, COL2, ... )] 서브 쿼리;

### UPDATE
> UPDATE table SET 수정할 칼럼1  = 수정될 값1 [, 수정할 컬럼2 = 수정될 값2] [WHERE 수정 대상 식별 조건식];

### DELETE
> DELETE [FROM] 테이블명 [WHERE 삭제 대상 식별 조건식];

- where 절 없을 경우 테이블 전체 데이터삭제

### MERGE 
- 데이터 수정/ 삭제/ 입력을 한번에 하는 쿼리
- 여러 DML문을 단일문으로 대체 가능하다.

        MERGE 타겟 테이블
        USING 소스 테이블
        ON (조인 조건식)
        WHEN MATCHED THEN
            UPDATE
            SET 수정할 컬럼1 = 수정될 새로운 값 1
        WHEN NOT MATCHED THEN
            INSERT [(칼럼1, 칼럼2, ...)]
            VALUES (값1, 값2, ...) ;
            
### DDL과 DML 명령어 처리 방식 차이
- DDL은 데이터구조의 변경이 즉각 반영
- DML은 COMMIT이 필요
- 데이터 삭제에 있어서 DELETE는 로그를 저장하므로 ROLLBACK 가능
- TRUNCTATE TABLE 사용시 데이터 로그가 없어 ROLLBACK 불가능

## TCL
### 트랜잭션 개요
- 데이터 베이스의 논리적 연산단위
- 하나의 트랜잭션에는 하나 이상의 SQL 문장이 포함
- 분할할 수 없는 최소의 단위
- ALL or NOTHING 

### ACID
1. Atomicity : 트랜잭션으로 정의된 연산은 최소 원자성을 유지해야하므로, 모두 성공하던지 모두 실패하던지 해야한다(All or Nothing)
2. Consitency : 트랜잭션이 실행되기전 데이터가 잘못되지 않았다면, 트랜잭션 수행후에도 데이터는 무결해야한다.
3. Isolation : 트랜잭션이 실행되는 도중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다.
4. Durability : 트랜잭션이 성공한다면 그 갱신내역은 영속화되어야 한다.

### COMMIT
> 트랜 잭션 완료

- 변경사항이 데이터베이스에 반영
- 이전 데이터는 영원히 잃어버림
- 모든 사용자는 결과를 볼 수 있음
- 관련된 행에 대한 잠금이 풀리고, 다른 사용자들이 행을 조작할 수 있게된다.

### ROLLBACK
> COMMIT을 하지 않았다면 실행한 SQL문 이전 COMMIT시점으로 되돌리는 명령어


- 데이터에 대한 변경사항 취소
- 데이터가 트랜잭셔 시작 이전으로 되돌려진다
- 관련된 행의 LOCKING 해제


### SAVEPOINT
> ROLLBACK 할 지점을 임의로 지정하는 명령어

## DDL

### CREATE TABLE

        CREATE TABLE TABLE_NAME (
         칼럼명1 데이터유형 [기본값] [NOTNULL],
         칼럼명2 데이터유형 [기본값] [NOTNULL],
         칼럼명3 데이터유형 [기본값] [NOTNULL],
        );
        
            
#### 주의할 규칙
 - 테이블 명은 객체를 의미할 수 있는 적절한 이름일것, 가능한 단수형을 권고
 - 테이블명은 다른 테이블의 이름과 중복되지 않아야한다
 - 한 테이블 내에서는 칼럼명 중복x
 - 반드시 숫자가 아닌 문자로 시작
 - `-` 사용 금지
 
#### 제약 조건(CONSTRAINT)

| 구분 | 설명 |
| --- | --- |
| PRIMARY KEY | 테이블에 저장된 행 데이터를 고유하게 식별하기 위한 기본키 정의<br> 하나의 테이블에 하나의 기본키 <br> 자동으로 UNIQUE 인덱스 생성, NOTNULL |
|  UNIQUE| 저장된 행 데이터를 고유하게 식별하기 위한 고유키 정의 |
| NOT NULL | NULL 금지 |
| CHECK | 입력할 값을 제한, T/F로 평가 가능한 논리식이 와야됨 |
| FOREIGN KEY |   테이블간 관계를 정의하기 위해 기본키를 다른 테이블의 외래키로 복사하는 경우 외래키 생성, 참조 무결성 제약옵션을 선택할 수 있다.|


#### ALTER TABLE
1. ADD COLUMN
    - 기존 테이블에 컬럼 추가

            ALTER TABLE 테이블명
            ADD (추가할 컬럼명1 데이터 유형 [기본값] [NOT NULL]
            [, ...]);
            
           
2. DROP COLUMN
    - 테이블에 필요없는 컬럼 삭제

            ALTER TABLE 테이블명
            DROP (삭제할 컬럼명1 [, 삭제할 컬럼명2,...]);
            
3. MODIFY COLUMN
    - 테이블에 존재하는 칼럼에 대해 칼럼의 데이터유형, 디폴트값, 제약조건등을 변경

            ALTER TABLE 테이블명
            MODIFY ( 컬럼명1 데이터 유형 [기본값] [NOT NULL]
            [, ...]);
            
    - 해당 칼럼의 크기를 늘릴수는 있지만, 테이블에 데이터가 존재한다면 칼럼의 크기를 줄이는데는 제약이 있다.
    - 해당 칼럼이 NULL만 존재한다면 데이터 유형을 변경할 수 있다.
    - DEFULT 변경은 이후 발생하는 행 삽입에만 영향
    - NULL이 없을경우에만 NOT NULL 제약조건 추가가능

4. RENAME COLUMN
    - 칼럼명 변경시 사용

            ALTER TABLE 테이블명
            RENAME COLUMN 기존 컬럼명 TO 새로운 컬럼명;
            
5. DROP CONSTRAINT
    - 제약조건 삭제

            ALTER TABLE 테이블명
            DROP CONSTRAINT 제약조건명;
            
6. ADD CONSTRAINT
    - 제약 조건 추가
    

            ALTER TABLE 테이블명
            ADD CONSTRAINT 제약조건명 제약조건 (칼럼명);
            

### RENAME TABLE

- 테이블 명 변경

> RENAME 기존 테이블명 TO 새로운 테이블명;

### DROP TABLE
- 테이블 삭제

 > DROP TABLE 테이블명 [CASCADE CONSTRAINT];

### TRUNCATE TABLE
- 해당 테이블에 들어 있던 모든 행들이 제거, 제약조건등은 남아있음

> TRUNCATE TABLE 테이블명;

## DCL
- 유저 권한을 생성하고 제어하는 명령어
