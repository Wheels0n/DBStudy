# SQL 기본 문법

여기서는 너무나도 익숙한 SQL 구문을 복습겸 정리 할 것이다. SQL Server기준이고  
SSMS를 사용했다.

## SQL 소개

- SQL(Structured Query Langauger)

  - 관계 데이터베이스를 위한 표준 질의어
  - 1974년에 IBM 연구소에서 데이터베이스 시스템. "시스템 R"을 질의하기 위해서 만들어진 구조화된 언어
  - 미국 표준 연구소인 ANSI와 국제 표준화 기구인 ISO에서 표준화 작업을 진행

- SQL의 분류
  - 데이터 정의어(DDL)
    - 테이블을 생성하고 변경, 제거하는 기능을 제공
    - CREATE/ALTER/DROP
  - 데이터 조작어(DML)
    - 테이블에 새 데이터를 삽입하거나, 테이블에 저장된 데이터를 수정, 삭제, 검색하는 기능을 제공
    - SELECT/INSERT/UPDATE/DELETE
  - 데이터 제어어(DCL)
    - 보안을 위해 데이터에 대한 접근 및 사용 권한을 사용자별로 부여하거나 취소하는 기능을 제공

## 기본 문법

표준이 있지만 각 제품마다 자기만의 것이 추가 되어있다. 우리가 쓸 SQL은 T-SQL이다.
참고로 문법을 대소문자 섞어도 되기는 된다.

### SELECT

질의를 하는 가장 간단한 방법이다. SELECT문에서의 논리적 처리 순서는 다음과 같다.

1. FROM
2. ON
3. JOIN
4. WHERE
5. GROUP BY
6. WITH CUBE or WITH ROLLUP
7. HAVING
8. SELECT
9. DISTINCT
10. ORDER BY
11. TOP

```sql
SELECT 항목 FROM 테이블;
SELECT 항목1, 항목2, 항목3 FROM 테이블;
SELECT * FROM 테이블;
SELECT 항목 AS 별명 FROM 테이블;
```

- 항목란에 \*을 넣으면 모든 컬럼을 가져온다.
- 원하는 컬럼만 ,으로 구분해서 가져 올 수 도있다.
- 컬럼을 원하는 이름으로 출력하려면 As를 사용한다.

WHERE문으로 추가 조건을 부가 할 수 있다.

```sql
SELECT 나이 FROM 테이블 where 나이 = 50;
SELECT 나이, 성별 FROM 테이블 where 나이 = 50 and 성별 = '남성';
SELECT 나이, 성별 FROM 테이블 where 나이 = 50 or 성별 = '여성';
SELECT 전화번호 FROM 테이블 where 전화번호 IS NOT NULL;
```

- 참고로 '=='가 아닌 '='로 해야한다. 다른 건 똑같이 '!='이다.
- AND/OR으로 조건문을 조합 가능하지만 '&&'와 '||'가 아닌 AND/OR를 써야함.
- AND와 OR중의 AND의 우선순위가 더 높다. 물론 ()를 통해 순서를 바꿀수는 있다.
- NULL인지 아닌지 구분하려면 'IS NULL' 또는 'IS NOT NULL'을 써야한다.

문자열이 완전히 일치하지 않더라도 포함하고 싶을 떄가 있다. 이떄는 LIKE구문을 사용한다.

```sql
SELECT 이름 FROM 테이블 WHERE 이름 LIKE 'J%'/*Jason, Joe...etc*/
SELECT 이름 FROM 테이블 WHERE 이름 LIKE '_ean'/*Sean, Dean...etc*/
SELECT 이름 FROM 테이블 WHERE 이름 LIKE '[C-P]arsen'/*Carsen, Karsen...etc*/
SELECT 이름 FROM 테이블 WHERE 이름 LIKE 'de[^l]%'/*dennis, deab... etc*/
```

- % : 임의의 문자열 아무거나
- \_ : 임의의 문자 1개
- [] : 정해진 범위의 문자 아무거나
- [^] : 정해진 범위의 문자를 포함하지 않는 아무거나

ORDER BY를 통해서 정렬또한 가능하다.

```sql
SELECT 생년 FROM 테이블 ORDER BY 생년;
SELECT 생년 FROM 테이블 ORDER BY 생년 DESC;

SELECT 생년 FROM 테이블 ORDER BY 생년 DESC, 생일 DESC;


SELECT 생년 FROM 테이블 ORDER BY 생년 DESC, 생일 DESC
OFFSET 100 ROWS FETCH NEXT 100 ROW ONLY;
```

- ASC가 기본이다. 내림차는 DESC를 붙여야한다.
- 여러항을 기준으로도 가능하다. ,로 구분해서 추가 가능
- NULL 값은 최솟값으로 취급된다.
- 일정 개수 생략하려면 OFFSET 구문을 쓰면 된다. OFFSET m ROWS FETCH NEXT n ROW ONLY  
   m개 생략후 n개 가져오기. 표준에도 있음

TOP 구문으로 N개의 결과만 가져올 수 있다. 표준은 아님.

```sql
SELECT TOP(10) 적립금 FROM 테이블 ORDER BY 적립금 DESC;
SELECT TOP 5 PERCENT FROM 테이블 ORDER BY 적립금 DESC;
```

### 수치 및 문자열

- 괄호는 선택 사항이다
- 만약 ORDER BY와 같이 사용하면 상위/하위 N개의 결과를 가져온다.
- PERCENT 구문으로 개수 대신 비율로도 질의 가능하다.

+-\*/%같은 기본 산술 연산자도 지원한다. 이것도 우선순위가 있으니 주의.  
자체적으로 ROUND, POWER, COS같은 함수들도 있으니 질의문에 포함 가능하다.

```sql
SELECT 2025 - 생년 FROM 테이블;
```

문자열은 작은 따옴표로 '문자열'로 표기한다. 유니코드로하려면 문자열 앞에 N을 붙인다. N'한글'  
문자열 지원함수도 여렀있다. SUBSTRING, CONCAT, TRIM등...

### 시간 날짜

시간관리를 위한 타입도 따로 존재한다.

- DATE : 연월일
- TIME : 시분초
- DATETIME: 연월일시분초

문자열리터럴을 이들 타입으로 캐스팅하는 방식도 있다.

```sql
SELECT CAST ('2022-05-08 12:35:29.1234567 +12:15' AS TIME (7)) AS 'time',
       CAST ('2022-05-08 12:35:29.1234567 +12:15' AS DATE) AS 'date',
       CAST ('2022-05-08 12:35:29.123' AS SMALLDATETIME) AS 'smalldatetime',
       CAST ('2022-05-08 12:35:29.123' AS DATETIME) AS 'datetime',
       CAST ('2022-05-08 12:35:29.1234567 +12:15' AS DATETIME2 (7)) AS 'datetime2',
       CAST ('2022-05-08 12:35:29.1234567 +12:15' AS DATETIMEOFFSET (7)) AS 'datetimeoffset';
/* Result
time	            12:35:29.1234567
date	            2022-05-08
smalldatetime	    2022-05-08 12:35:00
datetime	        2022-05-08 12:35:29.123
datetime2	        2022-05-08 12:35:29.1234567
datetimeoffset	    2022-05-08 12:35:29.1234567 +12:15
/*
```

현재 언어 설정이 기본 날짜 포맷을 결정한다. 바꾸려면 SET LANGUAGE나 SET DATEFORMAT을 써야한다.

```sql
SET DATEFORMAT mdy;
```

역시 시간&날짜 타입을 위한 지원 함수가 있다. 일자를 가져오려면 GETDATE(), CURRENT_TIMESTAMP()를 이용하면 된다.  
주의할 점으로 사용자 환경 기준이라 UTC(GMT표준 시계)타임으로 작업해야한다. GETUTCDATE()를 쓸 것

DATEADD(), DATEADD(), DATEDIFF(단위, 시작, 끝)등으로 날짜 계산이 가능하고 DATEPART(단위, 문자열)로 특정 부분만 가져올수도 있다.

### CASE문

다른 프로그래밍 언어의 switch문과 같다. 문장이나 절에서 쓰일 수 있다. 단, CASE문으로는 실행의 흐름을 컨트롤 할 수는 없다.  
CASE문은 순차적으로 조건문을 평가하다가 참이되면 멈춘다. 어떠한 조건도 통과하지 못하면 ELSE문으로 끝에 처리 가능하다.  
만약 else가 없다면 어떤한 조건도 통과 못하면 NULL이 반환된다.

```SQL
CASE
     WHEN 조건 THEN 값
     WHEN 조건 THEN 값
     WHEN 조건 THEN 값
     WHEN 조건 THEN 값
     ELSE 문장
END

CASE
     WHEN 컬럼 (조건) THEN 값
END
```

### 집계함수

- APPROX_COUNT_DISTINCT
- AVG
- CHECKSUM_AGG
- COUNT
- COUNT_BIG
- GROUPING
- GROUPING_ID
- MAX
- MIN
- STDEV
- STDEVP
- STRING_AGG
- SUM
- VAR
- VARP

집계함수는 값들의 집합에 대해 계산을 하여 하나의 값을 반환한다. 보통 SELECT문의 GROUP BY절과 같이 쓰인다.  
COUNT(\*)를 제외하면 집계함수는 널 값을 무시한다. DISTINCT로 중복 데이터는 제외 할 수 있다. ALL이 기본이다.

```sql
SELECT COUNT(DISTINCT Title)
FROM HumanResources.Employee;

SELECT AVG(DISTINCT ListPrice)
FROM Production.Product;

SELECT MAX(TaxRate)
FROM Sales.SalesTaxRate;

SELECT MIN(TaxRate)
FROM Sales.SalesTaxRate;

SELECT Color, SUM(ListPrice)AS TotalList,
       SUM(StandardCost) AS TotalCost
FROM dbo.DimProduct
GROUP BY Color
ORDER BY Color;

```

### GROUP BY

질의 결과를 그룹당 하나씩 반환하게 만드는 절이다.

```sql
SELECT Country, Region, SUM(sales) AS TotalSales
FROM Sales
GROUP BY Country, Region;
```

- GROUP BY절에 나온 컬럼이나 집계함수아니면 결과 컬럼으로 지정할 수 없다.
- WHERE문 그룹 짓기 전에 필터링, HAVING은 그룹을 필터링 함에 유의.
- 그룹 지을 기준 컬럼으로 여러 개를 추가 할 수 있다.

### INSERT

```sql

INSERT INTO Production.UnitMeasure
VALUES (N'FT', N'Feet', '20080414');

INSERT INTO Production.UnitMeasure (Name, UnitMeasureCode,
    ModifiedDate)
VALUES (N'Square Yards', N'Y2', GETDATE());
```

- 기본적으로 테이블 컬럼의 순서의 맞추어서 값을 기입해야한다.
- 그렇지 않다면 컬럼()로 직접 순서를 기입해 줘야한다.

### DELETE

하나 이상의 행을 테이블로부터 삭제한다.

```sql
DELETE FROM Sales.SalesPersonQuotaHistory;

DELETE FROM Production.ProductCostHistory
WHERE StandardCost > 1000.00;
```

- WHERE문이 없으면 해당 테이블의 모든 데이터를 지운다.

### UPDATE

테이블의 행의 값을 갱신한다.

```sql
UPDATE Person.Address
SET ModifiedDate = GETDATE();

UPDATE Production.Product
SET Color = N'Metallic Red'
WHERE Name LIKE N'Road-250%' AND Color = N'Red';
```

- WHERE문이 없으면 해당 테이블의 모든 데이터를 갱신한다.

### 서브 쿼리

SQL 명령문(SELECT, INSERT, UPDATE, DELETE) 또는 다른 서브 쿼리 안에 중첩된 쿼리이다. 서브 쿼리는
표현식이 허용 되면 어디든 지 사용가능하다. Inner Query 또는 Inner Select라고도 한다.

보통 이 서브 쿼리는 문법 상으로 동등한 다른 구문과 성능차이가 없다. 만약 존재 여부 자체 확인이 필요한  
경우 조인이 더 좋은 성능을 내기도 한다.

```sql
SELECT [Name]
FROM Production.Product
WHERE ListPrice =
    (SELECT ListPrice
     FROM Production.Product
     WHERE [Name] = 'Chainring Bolts' );

```

- 서브 쿼리는 WHERE 나 HAVING 절에 중첩 되어 질 수 있다.
- 최대 32단계까지 중첩 가능하지만 실제 한계는 가용메모리 자원이나 표현식의 복잡도에 달렸다.
- 서브쿼리에서 참조하는 테이블은 outer 쿼리의 컬럼으로 사용 될 수 없다.

```sql
SELECT [Name]
FROM Production.Product
WHERE ProductSubcategoryID IN
    (SELECT ProductSubcategoryID
     FROM Production.ProductSubcategory
     WHERE [Name] = 'Wheels');
```

- IN(또는 NOT IN)이랑 같이 쓰이면 0개이상의 값을 가진 리스트를 결과로 반환한다.

```sql
SELECT CustomerID
FROM Sales.Customer
WHERE TerritoryID =
    (SELECT TerritoryID
     FROM Sales.SalesPerson
     WHERE BusinessEntityID = 276);
```

- ANY나 ALL 같은 것을 수식 받지 않는 비교 연산자랑 같이 쓰이면 오로지 단일 값만을 반환한다.
- 이경우 서브 쿼리는 GROUP BY 나 HAVING 절에 중첩 되어 질 수 있다.

```sql
SELECT [Name]
FROM Production.Product
WHERE EXISTS
    (SELECT *
     FROM Production.ProductSubcategory
     WHERE ProductSubcategoryID =
            Production.Product.ProductSubcategoryID
        AND [Name] = 'Wheels');
```

- EXISTS와 같이 쓰이면 서브쿼리는 마치 존재 테스트처럼 동작한다. 단순히 TRUE/FALSE만 반환하고 데이터는 생성하지 않는다.

### CREATE

CREATE로 만들 수 있는 게 수 십개가 되는 데 자주 쓰는 DB와 테이블 생성만 나열해 보았다.

```sql
CREATE DATABASE mytest;
CREATE TABLE 이름(열이름, 타입[기본값][NULL|NOT NULL],..)
```

### DROP

마찬 가지로 드랍 대상은 많지만 자주 쓰는 테이블만 보자.

```sql
DROP TABLE ProductVendor1;
```

### ALTER

자주 쓰는 테이블만 살펴보자. 테이블을 수정한다는 건 컬럼 또는 제약을 드롭, 추가, 변경 한다는 것이다.

```sql
ALTER TABLE dbo.doc_exa ADD column_b VARCHAR(20) NULL ;
ALTER TABLE dbo.doc_exb DROP COLUMN column_b ;
ALTER TABLE MyTable ALTER COLUMN NullCOl NVARCHAR(20) NOT NULL;
```

### UNION

두 쿼리의 결과를 하나의 결과로 부텽 넣는 다. 중복 데이터 포함은 UNION ALL로 한다. JOIN은 두 테이블의 열로  
구성된 결과 행을 생성하는 반면 UNION은 두 테이블에서 가져온 열로 개별 행으로 만들지는 않는다. 모든 쿼리에서  
 열의 개수와 순서가 동일 해야하고 데이터 타입이 호환되어야한다.

```sql
SELECT ProductModelID, Name
FROM Production.ProductModel
WHERE ProductModelID NOT IN (3, 4)
UNION
SELECT ProductModelID, Name
FROM dbo.Gloves
ORDER BY Name;
```

### INTERSECT & EXCEPT

두 쿼리의 결과에서 고유한 행만 반환한다. EXCEPT는 우측 인풋 쿼리의 결과에 없는 좌측 인풋 쿼리 결과만을,
INTERSECT는 양측에 존재하는 고유 행을 반환한다. 모든 쿼리에서 의 개수와 순서가 동일 해야하고 데이터 타입이  
 호환되어야한다.

```sql
SELECT ProductID
FROM Production.Product
INTERSECT
SELECT ProductID
FROM Production.WorkOrder ;

SELECT ProductID
FROM Production.Product
EXCEPT
SELECT ProductID
FROM Production.WorkOrder ;
```

### JOIN

SQL 서버는 다음과 같은 논리 조인 연산을 지원한다. 물리 조인은 성능 튜닝 파트에서 볼 것이다.

- Inner join

  - 두 테이블을 조인할 떄, 두 테이블에 모두 지정한 열의 데이터가 있어야 한다.

  ```sql
  SELECT <열 목록>
  FROM <첫 번째 테이블>
      INNER JOIN <두 번째 테이블>
      ON <조인 조건>
  [WHERE 검색 조건]
  ```

- Outer join
  - 두 테이블을 조인할 때, 1개의 테이블에만 데이터가 있어도 결과가 나온다.
  ```sql
  SELECT <열 목록>
  FROM <첫 번째 테이블(LEFT 테이블)>
    <LEFT | RIGHT | FULL> OUTER JOIN <두 번째 테이블(RIGHT 테이블)>
     ON <조인 조건>
  [WHERE 검색 조건]
  ```
  - LEFT(왼쪽 테이블 모든값이 출력), RIGHT(우쪽 테이블 모든값이 출력), FULL(왼쪽 외부 조인과 오른쪽 외부 조인) 외부 조인이 있다.
- Cross join

  - 한 테이블의 행을 다른 테이블의 모든 행과 조인 시킨다. 결과는 m\*n개가 된다. 카테시안 곱이라고도 한다.

  ```sql
  SELECT *
  FROM <첫 번째 테이블>
      CROSS JOIN <두 번째 테이블>
  ```

### 변수와 흐름 제어

SQL에서 변수 사용이 가능하다. 변수 선언은 다음과 같다.

```sql
DECLARE @변수명 타입
DECLARE @변수명 타입 = 값
```

변수 값을 부여하는 방법은 다음과 같다.

```sql
SET @변수명 = 값
```

변수 스코프는 선언 된 시점 부터 선언 된 배치나 stored procedure의 끝까지 지속 된다. 배치를
구분하려면 GO를 사용하면 된다. 아래의 경우 에러가 발생한다.

```sql
GO

DECLARE @MyVariable INT;

SET @MyVariable = 1;
GO

SELECT BusinessEntityID,
    NationalIDNumber,
    JobTitle
FROM HumanResources.Employee
WHERE BusinessEntityID = @MyVariable;
```

SQL에서도 조건문, 반복문을 통한 흐름 제어가 가능하다.

```sql
IF DATENAME(weekday, GETDATE()) IN (N'Saturday', N'Sunday')
    SELECT 'Weekend';
ELSE
    SELECT 'Weekday';
--
WHILE (
        SELECT AVG(ListPrice)
        FROM Production.Product
        ) < $300
BEGIN
    UPDATE Production.Product
    SET ListPrice = ListPrice * 2

    SELECT MAX(ListPrice)
    FROM Production.Product

    IF (
            SELECT MAX(ListPrice)
            FROM Production.Product
            ) > $500
        BREAK
    ELSE
        CONTINUE
END
```

- 조건 만족시 실행 할 구문은 BEGIN...END로 묶지 않는 이상 하나만 실행된다.
- CONTINUE/BREAK/GOTO도 있다.

### 윈도우함수

윈도우 함수는 윈도우 내의 각 행에 대한 값을 계산한다. 이 윈도우(질의 결과 내 사용 자 정의 행 집합)를 정의 하는 것은  
OVER절이다. OVER절은 행 집합의 순서와 파티션을 정의한다. 윈도우 함수들은 OVER절에 다음과 같은 인자들을 요구 할 수도  
있다. 인자를 명시 안하면 전체 데이터 결과에 적용된다. OVER절은 랭킹 함수, 집합 함수등과 같이 쓸 수 있다.

```sql
OVER (
       [ <PARTITION BY clause> ]
       [ <ORDER BY clause> ]
       [ <ROW or RANGE clause> ]
      )
```

- PARTITION BY : 파티션 기준이 될 컬럼으로 FROM절로부터 접근이 가능해야한다.
- ORDER BY : 정렬은 명시되었지만 ROWS나 RANGE가 명시 안 되면 RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW가 쓰인다.
- ROW or RANGE : ROWS 절은 파티 션 내 행의 수를 전후 행의 개수를 명시하여 제한한다. RANGE절은 파티션 내의 행의 수를  
  현재의 행의 값과 비교하여 값의 범위를 명시하여 논리적으로 제한한다.
- 일정 한 데이터 셋에 연산을 한다는 점에서 GROUP BY와 비슷해보이지만 윈도우 함수는 적용 후에도 개별 데이터를 보존한다.

#### 참조

- [데이터 베이스 원리와 응용\_11장](http://www.kocw.net/home/search/kemView.do?kemId=1163794)
- [MSDN : T-SQL](https://learn.microsoft.com/en-us/sql/t-sql/language-reference?view=sql-server-ver16)
- [MSDN : 검색 조건](https://learn.microsoft.com/en-us/sql/t-sql/queries/search-condition-transact-sql?view=sql-server-ver16)
- [MSDN : LIKE](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver16)
- [MSDN : ORDER BY](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-order-by-clause-transact-sql?view=sql-server-ver16)
- [MSDN : TOP](https://learn.microsoft.com/en-us/sql/t-sql/queries/top-transact-sql?view=sql-server-ver16)
- [MSDN : 사칙연산](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/arithmetic-operators-transact-sql?view=sql-server-ver16)
- [MSDN : 연산자 우선순위](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/operator-precedence-transact-sql?view=sql-server-ver16)
- [MSDN : 수학 함수](https://learn.microsoft.com/en-us/sql/t-sql/functions/mathematical-functions-transact-sql?view=sql-server-ver16)
- [MSDN : 문자열 함수](https://learn.microsoft.com/en-us/sql/t-sql/functions/string-functions-transact-sql?view=sql-server-ver16)
- [MSDN : 시간 타입](https://learn.microsoft.com/en-us/sql/t-sql/data-types/date-and-time-types?view=sql-server-ver16)
- [MSDN : 날짜 시간 타입 함수](https://learn.microsoft.com/en-us/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver16)
- [MSDN : CASE](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql?view=sql-server-ver16)
- [MSDN : 집계함수](https://learn.microsoft.com/en-us/sql/t-sql/functions/aggregate-functions-transact-sql?view=sql-server-ver16)
- [MSDN : SELECT - GROUP BY- Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-group-by-transact-sql?view=sql-server-ver16)
- [MSDN : INSERT](https://learn.microsoft.com/en-us/sql/t-sql/statements/insert-transact-sql?view=sql-server-ver16)
- [MSDN : DELETE](https://learn.microsoft.com/en-us/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver16)
- [MSDN : UPDATE](https://learn.microsoft.com/en-us/sql/t-sql/queries/update-transact-sql?view=sql-server-ver16)
- [MSDN : 서브쿼리](https://learn.microsoft.com/en-us/sql/relational-databases/performance/subqueries?view=sql-server-ver16)
- [MSDN : CREATE DATABASE](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver16&tabs=sqlpool)
- [MSDN : CREATE TABLE](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-transact-sql?view=sql-server-ver16)
- [MSDN : DROP TABLE](https://learn.microsoft.com/en-us/sql/t-sql/statements/drop-table-transact-sql?view=sql-server-ver16)
- [MSDN : ALTER TALBE](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql?view=sql-server-ver16)
- [MSDN : UNION](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/set-operators-union-transact-sql?view=sql-server-ver16)
- [MSDN : EXCEPT&INTERCET](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/set-operators-except-and-intersect-transact-sql?view=sql-server-ver16)
- [MSDN : JOIN](https://learn.microsoft.com/en-us/sql/relational-databases/performance/joins?view=sql-server-ver16)
- [Join 종류](https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/)
- [MSDN : 변수](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/variables-transact-sql?view=sql-server-ver16)
- [MSDN : 흐름 제어](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver16)
- [MSDN : OVER 절](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver16)
