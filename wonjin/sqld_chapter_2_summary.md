# SQL 기본
## SELECT
> SELECT [ALL/DISTINCT] 출력 대상 칼럼명....., FROM 출력대상 칼럼의 테이블명

### Alias 사용시 주의사항
- 공백이 들어가는 경우 `" "` 사용
- SQL Server의 경우 `" "`, `' '`, `[ ]` 가능

### 산술연산자와 합성 연산자
#### 산술 연산자
> +, -, *, /
- 수학의 4칙연산과 동일, 우선순위를 위한 `()` 적용 가능
#### 합성 연산자(Concatenation)
- 문자와 문자 연결시 `||`(Oracle) 또는 `+`(SQL  Server)
- CONCAT(st1, st2) 함수(Mysql도 가능)
- 칼럼과 문자, 또는 칼럼과 칼럼 연결

### SQL 문장 실행 순서

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

## 함수 - 단일행함수만
### 함수 분류
![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/edae1656-4d3b-4a4b-b8dc-89062446c14d.png)


### 단일행 함수 - 문자열 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/27f02e7b-f284-45ef-a67f-1513d2c54150.png)


![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/cd6fec3c-c62b-49e6-b6cc-19adf8e5deff.png)
### 단일행 함수 - 숫자형 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/af237faf-480a-4d7c-bab7-cb8d6eadbe69.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/48298974-6b90-454e-be45-63985b813f1e.png)


### 단일행 함수 - 날짜형 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/b79ee7ff-424d-45b0-8d80-ef9c30a4657d.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/f07d194b-a6a3-4427-9e58-3ec8d82dec4a.png)


### 단일행 함수 - 변환 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/4514ec71-6bb3-4521-8220-6b4d601fbaf8.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/7d28a33f-f6a4-4ca8-971d-26acff4e4fbe.png)

### 단일행 함수 - CASE 표현식
e.g)

    SELECT NAME,
           CASE
               WHEN SAL > 2000 THEN SAL
               ELSE 2000
           END AS REVISED_SAL
     FROM EMP   


- CASE - WHEN - ELS - END

- ORACLE은 `DECODE()`함수로도 같은 표현 가능 

### 단일행 함수 - NULL 관련 함수

#### NULL 의 특성
- 널값은 아직 정의되지 않은 값으로 0, 공백과는 다르다. 0은 숫자, 공백은 하나의 문자
- 테이블을 생성할때 NOT NULL / PRIMARY KEY로 정의되지 않은 모든 데이터 유형은 NULLABLE
- NULL값을 포함하는 연산의 경우 결과 값도 NULL 값이다. 모르는 데이터에 숫자를 더하거나 뺴도 결과는 마찬가지로 모르는 데이터인셈

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/53f570df-c070-4f3b-a3ae-d5ff68194a3d.png)


#### NVL/ISNULL(SQL Server)
- 결과값을 NULL이 아닌 다른 값을 얻고자 할때, NVL/ISNULL사용

- 데이터 조회시 컬럼이 NULL인것과 공집합(조회되는 데이터가 아예 없는것)은 다른 상황이다.
- NVL/ISNULL을 사용하더라도 공집합은 공집합으로 처리됨!
    
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT NVL (SAL, 1000) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 결과값없음
       
- 해당 경우 적절한 집계함수를 사용해 대처
     
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT MAX (SAL) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 1개의 행 선택, 빈 컬럼
       
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT NVL(MAX(SAL),1000) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 1개의 행 선택, 1000으로 출력
       

## WHERE

### 연산자 우선순위


| 연산자 우선순위 | 설명 |
| --- | --- |
| 1 | 괄호 () |
|  2| 비교연산자, SQL 연산자 |
|  3| NOT 연산자 |
|  4| AND |
|  5|  OR|

### SQL 연산자

#### IN
아래 처럼 사용도 가능
> WHERE (JOB, DEP_NO) IN (('MANAGER', 20), ('EMP', 30));

#### LIKE

- 와일드 카드 `%`, `_`
    - `%` : 0개 이상의 어떤 문자
    - `_` : 1개인 단일 문자

## GROUP BY, HAVING 절
### 다중행함수 - 집계함수
- 여러 행들의 그룹이 모여, 그룹당 단 하나의 결과를 돌려주는 함수
- GROUP BY 절은 행들을 소그룹화한다
- SELECT, HAVING, ORDER BY 절에서 사용가능

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/3ee8d4cb-2cb6-4671-bad5-f6e87c181063.png)

- GROUP BY 절 없이도 사용 가능!(테이블 전체도 하나의 그룹이므로)
- `COUNT(col1)`에서 `col1`에 NULL이 존재하는 경우 카운팅 x

### GROUP BY
- 데이터들을 작은 그룹으로 분류해 소그룹에 대한 항목별 통계정보를 얻을때 사용
- GROUP BY 절을 통해 소그룹별 기준을 정한 뒤, SELECT 절에 집계함수 사용
- 집계함수의 통계정보는 NULL 값을 가진행을 제외하고 수행
     - e.g) SUM 연산에서는 NVL을 사용해 NULL을 0으로 바꾸는 연산이 불필요하지만, AVG 같은경우 NVL을 통해 0으로 만들어야 제대로된 평균값을 구할수 있음
- GROUP BY 절에선 ALIAS 사용 불가
- WHERE 절 실행 후 GROUP BY 절 실행
- 따라서 GROUP BY 이후 조건절 필요시 HAVING 절 사용

## ORDER BY 절
### 정렬 데이터에 NULL 존재시
- ORACLE :  NULL 값을 가장 큰값으로 간주
- SQL Server, MYSQL : NULL 값을 가장 작은 값으로 간주

## JOIN
### EQUI JOIN
> 등가 조인

두개의 테이블 간에 컬럼값들이 정확하게 일치하는 경우 사용되는 방법

       SELECT T1.COL1, T2.COL1
       FROM T1 JOIN T2
           ON T2.COL2 = T1.COL2
           
### NON EQUI JOIN
> `=`연산자가 아닌 다른 연산자들을 사용해 JOIN 수행(Between, >, >=, <,<= 등)

- 예를들어 연봉컬럼(SAL)이 있는 회원테이블과 연봉 범위에 따라 연봉 등급이 있는 연봉등급 테이블 두개가 존재한다고 가정할 때,

        SELECT A.NAME, A.SAL, B.GRADE
        FROM EMP A, SALGRADE B
        WHERE A.SAL BETWEEN B.LOW_SAL AND B.HIGH_SAL
        
위와같은 join도 가능

### JOIN 구문 표현법

#### 1. INNER JOIN

- JOIN - ON 표현

#### 2. NATURAL JOIN
- FROM 절에 `NATURAL JOIN` 표시하면 알아서 EQUI JOIN 실행
- ORACLE 만 가능
- 가독성과 유지보수 차원에서 안쓰는것 권장
- `USING`가 `ON`과 같은 기능 수행

#### CROSS JOIN

- 양 테이블을 모두 조인하는것
- M * N 건의 데이터 조합 발생

#### OUTER JOIN
- 오라클은 조인 컬럼뒤에 `+` 표시 추가로 OUTER JOIN 실시 가능하지만 ANSI 표준을 사용하자
- LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN



