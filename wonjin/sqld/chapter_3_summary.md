# SQL 활용
## 서브 쿼리
> 하나의 SQL 문안에 포함돼 있는 또다른 SQL 문. 서브쿼리는 메인쿼리가 서브쿼리를 포함하는 구조의 종속적인 관계

- 서브쿼리는 괄호로 감싸서 기술
- 서브쿼리는 단일 행 또는 복수행 비교 연산자와 함께 사용 가능
- 중첩서브쿼리, 스칼라서브쿼리에서는 ORDER BY 사용 불가능

### 단일행 서브쿼리
> 서브쿼리의 결과가 반드시 한건이하로 나오는 서브쿼리 

- 단일행 비교 연산자와 함께 사용 가능
- e.g) A회원이 포함한 팀이 맴버를 찾는 구분
        
        SELECT *
        FROM MEMBER
        WHERE TEAM_ID = (SELECT TEAM_ID
                            FROM MEMBER
                            WHERE MEMBER_NAME = 'JINIA')
        ORDER BY MEMBER_NAME;
        

### 다중행 서브쿼리
> 서브쿼리 결과가 2건이상 반환되는 케이스

- 다중행 비교 연산자와 함께 사용해야한다(IN, ALL, ANY, SOME)
- e.g) 단일행 서브쿼리 예시에서 JINIA 가  동명이인이 존재, 서로 다른팀인경우 

        
        SELECT *
        FROM MEMBER
        WHERE TEAM_ID IN (SELECT TEAM_ID
                            FROM MEMBER
                            WHERE MEMBER_NAME = 'JINIA')
        ORDER BY MEMBER_NAME;
        
   - 실행결과 : 두개의 데이터가 조회됨

### 다중 칼럼 서브 쿼리
> 서브쿼리의 결과로 여러개의 컬럼이 반환되며, 메인쿼리의 조건과 동시에 비교

- SQL Server에서는 지원하지 않는 문법

        
        SELECT *
        FROM MEMBER
        WHERE (TEAM_ID, HEIGHT) IN (SELECT TEAM_ID, MIN(HEIGHT)
                            FROM MEMBER
                            GROUP BY TEAM_ID)
        ORDER BY MEMBER_NAME;
        

### 연관 서브 쿼리

> 서브 쿼리 내에 메인 쿼리 칼럼이 사용된 서브 쿼리

### 스칼라 서브 쿼리
> SELECT 절 위치에서 사용하는 쿼리로 한행, 한 칼럼만을 반환하는 쿼리

### 인라인뷰
> FROM 절에서 사용하는 서브쿼리
- 마치 테이블처럼 사용 가능

## 집합 연산자

> 두개 이상의 테이블에서 조인을 사용하지 않고 연관된 데이터를 조회하는 방법


| 집합 연산자 |연산자의 의미  |
| --- | --- |
| UNION | 개별 SQL 에 대한 합집합, 중복 하나로 압축 |
| UNION ALL | 개별 SQL 에 대해 합집합시 중복된 행도 그대로 표시 |
| INTERSECT | 개별 SQL 에 대한 교집합 |
| EXCEPT | 차집합 연산(ORACLE- MINUS, SQL Server - EXCEPT) |

- INTERSECT 연산은 WHERE EXISTS 또는 IN 서브 쿼리로 변경 가능


## 다중행 함수 - 그룹 함수
> 하나 이상의 행을 그룹으로 묶어 연산하여 하나의 결과를 나타내는 것

### 그룹함수의 필요성

- 결산 개념의 업무를 가지는 원가나 판매 시스템의 경우는 소계, 중계, 합계, 총 합계 등 여러 레벨의 결산 보고서를 만드는 것이 중요 업무. 
- 개발자들이 이런 보고서를 작성하기 위해서는 SQL이 포함된 3GL으로 배치 프로그램을 작성하거나, 레벨별 집계를 위한 여러 단계의 SQL을 UNION, UNION ALL로 묶은 후 하나의 테이블을 여러 번 읽어 다시 재정렬하는 복잡한 단계를 거쳐야함

- 하지만, 그룹 함수(GROUP FUNCTION)을 사용한다면, 하나의 SQL로 테이블을 한 번만 읽어서 빠르게 원하는 리포트를 작성 가능

### ROLLUP 함수
> SubTotal을 생성하기 위해 사용

- Grouping Columns의 수를 기준으로 N+1 레벨의 Subtotal이 생성된다.

       SELECT B.NAME, A.JOB, COUNT(*) AS EMP_CNT, SUM(A.SAL) AS SAL_SUM 
       FROM EMP A, DEPT B
       WHERE B.DEPTNO = A.DEPTNO
       GROUP BY ROLLUP (B.DNAME, A.JOB);
       

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/90ab6c8d-0457-478e-b05c-35da7b112b28.png)


### CUBE 함수
- ROLLUP 에서는 단지 가능한 Subtotal만 생성하지만, CUBE는 결합가능한 모든 값에 대해 다차원 집계를 생성
- CUBE 를 사용할 때는 내부적으로 Grouping Columns의 순서를 바꾸어서 또 한번의 쿼리를 추가 수행하며, Grand Total은 양쪽의 쿼리에서 모두 생성되고 중복제거 되므로, ROLLUP에 비해 시스템 연산이 많다.

### GROUPING SETS 함수
> GROUPING SETS는 여러 그룹핑 쿼리를 UNION ALL 한 것과 같은 결과를 만들어 다양한 소계 집합을 만들때 사용

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/1bef04e4-2eee-45a6-acdd-c095db5159b4.png)


## 윈도우 함수
### 개요
기존 관계형 데이터베이스는 칼럼과 칼럼간의 연산, 비교,연결이나 집합에대한 집계는 쉬운 반면, 행과 행간 관계정의, 비교,연산을 sql로 처리하기가 어려웠다.

Window Function을 사용하면 이런 행간 관계정의, 연산을 SQL로 해결하기 훨씬 쉬워진다.

### 종류
1. 그룹 내 순위 관련 함수(RANK)
2. 그룹 내 집계 관련 함수(기존 집계함수들)
3. 그룹 내행 순서 관련 함수
4. 그룹 내 비율 관련 함수
5. 선형분석을 포함한 통계분석 관련 함수

### 문법
> SELECT WINDOW_FUNCTION (ARGUMNETS) OVER ([PARTITION BY 칼럼] [ORDER BY 절] [WINDOWING 절]) FROM 테이블;

- WINDOW_FUNCTION : 기존에 사용하던 함수도 있고, 새롭게 Window 함수용으로 추가된 함수도 있음
- ARGUMENTS : 함수에 따른 인수
- PARTITION BY 절 : 전체 집합을 기준에 의해 소그룹으로 나눌 수 있음
- ORDER BY :  정렬
- WINDOWING : WINDOWING 절은 함수의 대상이 되는 행 기준의 범위를 강력하게 지정할 수 있다.

### 순위 함수

#### RANK
> ORDER BY 를 포함한 쿼리문에서 특정 항목에 대한 순위를 구하는 함수

        SELECT JOB, NAME, SAL, RANK () OVER (ORDER BY SAL DESC) AS SAL_RANK
        FROM EMP;
        
#### DENSE_RANK
> RANK 와 유사하나 동일한 순위를 하나의 건수로 취급

- RANK 가 1, 2,2,2, 3 순으로 간다면, DENSE_RANK는 1,2,2,2,5 순으로

#### ROW_NUMBER
> 동일 값에 대해 다른값을 부여
- 무조건 1,2,3,4,5...

### 일반 집계함수 -> 윈도우함수

> 파티션별 값을 구하기 위해 사용

        SELECT NO, NAME, SAL, SUM(SAL) OVER(PARTITION BY NO)
        FROM EMP;
        
- SUM, MAX,MIN,AVG,COUNT

### 그룹 내 행 순서 함수
- FIRST_VALUE
- LAST_VALUE
- LAG
- LEAD

### 그룹 내 비율 함수

- RATIO_TO_REPORT
- PERCENT_RANK
- CUME_DIST
- NTILE

### TOP N 쿼리
#### ROWNUM 슈도 컬럼
 - 오라클만의 문법

        SELECT NAME, SAL
        FROM EMP
        WHERE ROWNUM < 4
        ORDER BY SAL DESC;
        
#### TOP 절
> TOP (Expression) [PERCENT] [WITH TIES]

- SQL Server 문법
- Expression : 반환할 행 수를 지정하는 숫자


### ROW LIMITING
- ANSI 표준 SQL 문법
- ORACLE 12.1, SQL Server 2012부터 가능

## 계층형 질의와 셀프 조인
관계형 데이터베이스는 계층형 데이터를 저장할 때, 순환 관계 데이터 모델을 사용하여 저장 가능하고, 이를 조회하기 위해서 계층형 질의를 사용하거나, 셀프조인으로 계층형 구조를 흉내낼 수 있다.

### 셀프 조인
> 동일 테이블 사이의 조인

- 깊은 레벨의 노드를 조회하기위해서는 셀프 조인 반복이 필요
- Oracle과 SQL Server는 순환관계를 가진 데이터르 조회할수 있는 계층형 질의 기능을 제공한다.
- MYSQL은 계층형 쿼리가 존재하지 않음

### 계층형 질의
#### ORACLE

    SELECT ...
    FROM ...
     AND ...
    START WITH ..
            AND ..
    CONNECT BY ..
            AND ..
   
    
- 계층 표현을 위해 가상 컬럼 사용
    - LEVEL : 루트 데이터면 1, 그 하위 데이터면 2, 3...
    - CONNECT_BY_ISLEAF : 리프 데이터인지 여부 0/1
    - CONNECT_BY_ISCYCLE : 노드간 사이클링이 발생할경우, 옵셔날하게 출력 가능

#### SQL Server
- CTE(Common Table Expression)을 재귀호출하여 구현 가능

## PIVOT 절과 UNPIVOT절
- 행을 열로, 열을 행으로 회전시키는 문법

## 정규 표현식
### 정규 표현식 조건과 함수
- REGEXP_LIKE ()
- REGEXP_REPLACE ()
- REGEXP_SUBSTR()
- REGEXP_INSTR ()
- REGEXP_COUNT ()
