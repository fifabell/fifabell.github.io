---
title:  "MSSQL AtoZ"
excerpt: "mssql 팁"
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - mssql
tag:
  - database
last_modified_at: 2020-12-18T20:00:00+01:00
---

<br>

굳이 MSSQL를 국한시키는 것은 아니지만, <br>
기본적인 것들을 제외하고, MSSQL를 실제로 사용하면서 겪었던 몰랐던 부분들이나 메모했으면 좋겠다고 생각했던 부분들을 나름대로 정리했다.<br>

추가 업데이트는 계속 될 예정이다.<br>

<br>

## 0. mssql 속도개선 

> mssql 성능향상에 도움을 주는 기본적인 것들은 타 블로그에 정리가 잘되어있으므로 참고하자.<br>

[성능향상팁](https://m.blog.naver.com/islove8587/220605402338)<br>

이 중에서 인상깊었던 코드 : count <br>
```sql
SELECT rows FROM sys.sysindexes WHERE id = OBJECT_ID('테이블명') AND indid < 2
-- mssql내부의 시스템을 이용해 보다 빨리 count를 나타낼 수 있다.
```

## 1. 히스토리 나타내기

: 이 테이블을 포함해서 전체를 나타내는 방법을 소개. <br>

`HIS` 테이블 안에 <br>
`THIS` : `현재 코드`와 , `BEFORE` : `직전코드`가 저장되어 있을 때. <br>

```sql
SELECT * FROM CUR -- 현재 테이블 (PK,FK: CODE)
SELECT * FROM HIS -- 히스토리 테이블 (THIS, BEFORE)

SELECT
     ISNULL(B.THIS,A.CODE) 
FROM        CUR AS A
LEFT JOIN   HIS AS B ON A.CODE = B.BEFORE
GROUP BY ISNULL(B.THIS,A.CODE)

-- 직전코드에 코드가 있을 때 현재 코드로 바꿔줌으로써 전체 히스토리를 표시할 수 있음.
```

## 2. GROUP BY 여러 개

: GROUP BY가 1개일 땐 쉬울 수 있다. <br>
하지만 2개 이상일 땐, group by의 개념을 생각하면서 접근해야 한다.<br>
group by는 집계함수를 나타내기 위해 사용되어지는 그룹핑(또는 기준)이라고 보면 되는데,<br>
group by해서 나오는 데이터는 눈에 보이는 데이터 뿐만 아니라 그 데이터 안에 많은 데이터를 포함하고 있다고 생각해야한다.<br>

예를 들어,

```sql
SELECT AGE,COUNT(*) FROM MEMBER GROUP BY AGE 
```

를 하게되면, 각 나이마다 멤버들이 몇 명인지 나올 것이다. <br>
이를 단순히 이렇게되네.. 라고 생각하지 말고 저 숫자안에 MEMBER테이블의 나머지 아이들도 포함되어있다고 생각해야한다.<br>

> 가입년도, 가입월 기준으로 나이의 평균을 구해보자 <br>


```sql
SELECT * FROM MEMBER -- (가입년도: YEAR, 가입월: MONTH)

SELECT YEAR, MONTH, AVG(AGE)
FROM MEMBER
GROUP BY YEAR, MONTH
```

이처럼 기준이 되는 데이터를 모두 GROUP BY에 넣어주면 된다.<br>

이 외에도 COUNT,MAX,MIN,SUM 의 집계함수가 존재한다.<br>

## 3. '#' 임시테이블
mssql에는 임시테이블이라는 게 존재한다. <br>
가령, 많은 연산이 필요한 데이터를 뽑고 싶을 때, 이 임시테이블을 사용해 데이터를 분리하면 효율적인 속도로 데이터를 나타낼 수 있다.<br>

임시테이블을 만드는 2 가지 방법이 있으니 아래를 참고하자.

```sql
-- 방법 1. 일반 테이블처럼 만들기
CREATE #TEMP_TABLE (
    ENAME   VARCHAR(5),
    AGE     NUMERIC(3)
)

INSERT INTO #TEMP_TABLE
SELECT  ENAME, AGE 
FROM    MEMBER


-- 방법 2. 테이블 복사
SELECT * 
INTO #TEMP_TABLE 
FROM MEMBER
-- MEMBER테이블이 #TEMP_TABLE 로 복사가 된다.

-- 이 이후에는 #TEMP_TABLE을 마음대로 R,U,D할 수 있다.
```

'#'이 하나면 지역임시테이블, 두 개면 전역임시테이블로 사용이 가능하고, <br>
'@'를 붙여 테이블 변수로도 사요이 가능하다.<br>

## 4. 한 컬럼을 나눠 COUNT하기

! 백문이 코드일견 ! <br>

```sql
SELECT 	
	COUNT((CASE WHEN AGE < 30 THEN 1 ELSE NULL END)) '~20대',
	COUNT((CASE WHEN AGE / 10 = 3 THEN 1 ELSE NULL END)) '30대',
	COUNT((CASE WHEN AGE / 10 = 4 THEN 1 ELSE NULL END)) '40대',
	COUNT((CASE WHEN AGE / 10 = 5 THEN 1 ELSE NULL END)) '50대',
	COUNT((CASE WHEN AGE / 10 = 6 THEN 1 ELSE NULL END)) '60대',
	COUNT((CASE WHEN AGE > '69' THEN 1 ELSE NULL END))	 '70대~'
FROM MEMBER
```

## 5. WITH (NOLOCK)
0.속도개선에도 나오는 내용이지만, 중요해서 한 번 더 다뤄볼까 한다.<br>
기본적으로 MSsql은 SELECT시에 공유잠금이 걸린다. <br>
공유잠금이 걸려있는 동안에는 CRUD가 진행되지 않는다.<br>

이 때, SELECT 문에 WITH(NOLOCK)을 추가해주면 선행작업과 상관없이 바로 SELECT문이 수행된다.<br>

```sql
-- IN SELECT
SELECT * FROM MEMBER WITH(NOLOCK) 

-- IN PROCEDURE
CREATE PROCEDURE MEMBER_P
AS
    SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; -- 추가
    SET NOCOUNT ON; 
BEGIN

END    
```

## 6. 문자열 치환 ( REPLACE, STUFF ) 

! 백문이 코드일견 ! <br>

```sql
-- 1) REPLACE
--문법
REPLACE('문자열','치환예정문자','치환할문자')
--예시
REPLACE('ABCDEFG','DEF','XXX')
--예제
SELECT REPLACE (ENAME, '김', '나') AS '나씨이름' FROM MEMBER

-- 2) STUFF
--문법
STUFF('문자열','시작위치','크기','치환할문자')
--예시
STUFF('ABCDEFG',2,3,'XXX')
--예제
SELECT STUFF (ENAME,2,2,'바보') AS '바보들' FROM MEMBER
-- 모든이름 > 0바보 로 바꿈.
```

## 7. 세로 to 가로


세로로 쭉 나열된 데이터를 엑셀처럼 칼럼별로 나타내고 싶을 때가 있다.<br>
4.와 비슷한 내용이지만 한 번 더 다뤄본다.<br>

이런 데이터가 있다고 가정하자.<br>

```sql
SELECT * FROM MEMBER
--MEMBER에는 성(성별아님)을 저장하는 컬럼이 따로 있다. = SUNG
SELECT SUNG FROM MEMBER -- '김','정','이','박' 등등..
```

지금 이 테이블을 단순히 뽑게 되면 아래로 쭉 나올 것이다.<br>
이 테이블을 '성'별로 컬럼을 나누어 가로로 보고 싶을 때 아래와 같이 하면 된다.<br>

```sql
SELECT 
    MAX(CASE WHEN SUNG = '김' THEN 데이터 ELSE 데이터 END) AS '김씨데이터',
    MAX(CASE WHEN SUNG = '정' THEN 데이터 ELSE 데이터 END) AS '정씨데이터',
    MAX(CASE WHEN SUNG = '이' THEN 데이터 ELSE 데이터 END) AS '이씨데이터',
    MAX(CASE WHEN SUNG = '박' THEN 데이터 ELSE 데이터 END) AS '박씨데이터'
FROM MEMBER    
-- 데이터에 따라 집게함수는 달라질 수 있다.
```

