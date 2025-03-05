### `group by`

- 같은 값을 가진 행끼리 하나의 그룹으로 뭉쳐줌
- 보통 집계함수와 같이 사용
    - COUNT() : 행의 개수
    - AVG() : 행 안에 있는 값의 평
    - MIN() : 행 안에 있는 값의 최솟
    - MAX() : 행 안에 있는 값의 최댓값
    - SUM() : 행 안에 있는 값의 합

### [프로그래머스 | 서울에 위치한 식당 목록 출력하기](https://school.programmers.co.kr/learn/courses/30/lessons/131118)
```sql
SELECT 
    i.REST_ID, 
    i.REST_NAME, 
    i.FOOD_TYPE, 
    i.FAVORITES, 
    i.ADDRESS, 
    round(avg(r.REVIEW_SCORE), 2) as SCORE
from REST_INFO i
join REST_REVIEW r on i.REST_ID = r.REST_ID
where i.ADDRESS like '서울%'
group by 1
order by 6 desc, 4 desc;
```
- 주의점
  - SELECT 절에 avg(r.REVIEW_SCORE)라는 집계 함수 존재
  - 그룹화를 지정하지 않으면, 모든 행이 하나의 그룹으로 취급
  - 따라서 하나의 행만 반환하고 싶은 것이 아니면 `group by` 추가할 것

### `having`

- `where`은 집계함수와 함께 사용할 수 없으므로 `having` 을 사용

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5;
```

- `having`이 꼭 `group by`와 함께 써야하는 것은 아님
- 개별 그룹에 대한 집계가 필요한 경우에 `group by-having`절 필요
- `group by` 없이 `having`만 사용하면 전체 데이터를 하나의 그룹으로 봄
  
### [프로그래머스 | 조건에 맞는 사용자 정보 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/164670)

```sql
SELECT 
    u.USER_ID, 
    u.NICKNAME, 
    concat(u.CITY, ' ', u.STREET_ADDRESS1, ' ', u.STREET_ADDRESS2) as 전체주소,
    concat(left(TLNO, 3), '-', mid(TLNO, 4, 4), '-', right(TLNO, 4)) as 전화번호
from USED_GOODS_BOARD b
join USED_GOODS_USER u on b.WRITER_ID = u.USER_ID
group by u.USER_ID, u.NICKNAME, u.CITY, u.STREET_ADDRESS1, u.STREET_ADDRESS2, u.TLNO
having count(b.WRITER_ID) >= 3
order by 1 desc
```
- MySQL에서 `+`는 숫자 덧셈에 사용
- 문자열을 `+` 연결하려고 하면 MySQL은 문자열을 숫자로 변환하려고 시도하고, 변환할 수 없는 경우 0으로 처리
- 문자열 연결을 위해서는 반드시 `CONCAT` 함수 사용
  ### 예시
  ```sql
  left(TLNO, 3) + '-' + mid(TLNO, 4, 4) + '-' + right(TLNO, 4) # X
  CONCAT(left(TLNO, 3), '-', mid(TLNO, 4, 4), '-', right(TLNO, 4)) # O
  ```

날짜 조건은 `HAVING` 대신 `WHERE`절을 사용

`HAVING`은 그룹화 후의 조건에 사용
`WHERE`는 그룹화 전의 기본 필터링에 사용

```sql
SELECT 
    MCDP_CD as 진료과코드, 
    count(*) as 5월예약건수
from APPOINTMENT
where month(APNT_YMD) = 5
group by MCDP_CD
order by 2, 1
```

### `round(숫자, 자릿수)`

- 결과값을 지정한 자릿수까지 반올림해줌
- 평균을 소수점 두번째 자리에서 반올림 
    - round(avg(), 1)

    ```sql
    SELECT CAR_ID, round(avg(datediff(END_DATE, START_DATE) + 1), 1) as AVERAGE_DURATION
    from CAR_RENTAL_COMPANY_RENTAL_HISTORY
    group by CAR_ID
    having AVERAGE_DURATION >= 7
    order by AVERAGE_DURATION desc, CAR_ID desc
    ```
### `truncate(number, decimals)`
- 자리수 버리는 함수
### [프로그래머스 | 가격대별 상품 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131530)  
  ```sql
  SELECT truncate(PRICE, -4) as PRICE_GROUP, count(*) as PRODUCTS
  from PRODUCT
  group by 1
  order by 1
  ```

## ✨Date → Str 형 변환

---

### **`DATE_FORMAT(날짜, 출력 형식)`**

```sql
SELECT DATE_FORMAT('2019-09-16 20:23:12', '%Y-%m-%d')
```

### **`DATEDIFF(날짜1, 날짜2)`**

- 두 날짜 간의 차이를 구함
- 두 날짜간의 차이 + 1 을 해야 총 대여기간
    - 시작일이 5월 7일, 종료일이 5월 10일이라면, 대여기간은 4일
    - `datediff(END_DATE, START_DATE) + 1`

### **`in / not in`**

- `in`
    - in의 목록 값인 것을 반환
    
    ```sql
    SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE 
    from ANIMAL_INS
    where NAME 
    in ('Lucy', 'Ella', 'Pickle', 'Rogan', 'Sabrina', 'Mitty')
    order by ANIMAL_ID
    ```
    
- `not in`
    - in의 목록 값이 아닌 것을 반환
    
    ```sql
    SELECT * FROM Customers
    WHERE Country NOT IN ('Germany', 'France', 'UK');
    ```
    
    독일, 프랑스, 영국 출신이 아닌 모든 고객을 반환
    
### **`case 구문`**

- 칼럼에 조건부여
    - 조건이 true이면 결과 반환
    - else 조건이 충족되지 않으면 `Null` 반환
    - case 구문 형식
    
    ```sql
    CASE
        WHEN condition1 THEN result1
        WHEN condition2 THEN result2
        WHEN conditionN THEN resultN
        ELSE result
    END;
    ```
    
- 거래상태가 SALE 이면 판매중, RESERVED이면 예약중, DONE이면 거래완료 분류하여 출력

```sql
SELECT BOARD_ID, WRITER_ID, TITLE, PRICE, 
case STATUS 
    when 'SALE' then '판매중'
    when 'RESERVED' then '예약중'
    else '거래완료' end as STATUS 
from USED_GOODS_BOARD
where CREATED_DATE='2022-10-05'
order by BOARD_ID desc
```

## `LEFT`

- LEFT(문자열, 2)
    - 문자열의 왼쪽에서 2개의 문자를 추출

```sql
SELECT left(PRODUCT_CODE, 2) as CATEGORY, count(PRODUCT_ID) as PRODUCTS
from PRODUCT
group by CATEGORY # 상품 카테고리 코드별 상품 개수 구하기
order by CATEGORY
```
## `limit`

- 처음 n개 반환
- 가장 큰 물고기 10마리의 ID와 길이를 출력하는 SQL 문

```sql
select ID, LENGTH
from FISH_INFO
where LENGTH is not null
order by LENGTH desc, ID
limit 10
```

## `DISTINCT`
- 테이블 열에서 고유한 값만 반환
```sql
SELECT DISTINCT Country FROM Customers;
```

- 이름이 NULL인 경우는 집계하지 않으며 중복되는 이름은 하나로 침
- `DISTINCT` 자체가 `null`을 무시하므로 `WHERE NAME IS NOT NULL` 조건 필요없음

```sql
SELECT count(distinct NAME) as count
from ANIMAL_INS
```

### 활용법
- COUNT()
    - 데이터의 개수를 세는 함수
- `COUNT(DISTINCT [COL명])`
    - `DISTINCT`로 중복을 제거해 해당 COL의 데이터 종류 개수 파악
    - join을 하는 경우 중복된 데이터가 들어갈 수 있기에 `DISTINCT` 사용

```sql
SELECT 
    year(o.SALES_DATE) as YEAR, 
    month(o.SALES_DATE) as MONTH, 
    u.GENDER, 
    count(distinct u.USER_ID) as USERS
from USER_INFO u
join ONLINE_SALE o on u.USER_ID = o.USER_ID
where u.GENDER is not null
group by year(o.SALES_DATE), month(o.SALES_DATE), u.GENDER
# 년, 월, 성별 별로 집계
order by YEAR, MONTH, GENDER
# order by 1, 2, 3 으로 해도 무방
```

## `join`
- (INNER) JOIN
     - 두 테이블 모두에서 일치하는 값이 있는 레코드를 반환
- LEFT (OUTER) JOIN
     - 왼쪽 테이블의 모든 레코드를, 오른쪽 테이블의 일치하는 레코드를 반환
- RIGHT (OUTER) JOIN
     - 오른쪽 테이블의 모든 레코드를 반환하고, 왼쪽 테이블의 일치하는 레코드를 반환
- FULL (OUTER) JOIN
     - 왼쪽 또는 오른쪽 테이블 중 하나에 일치하는 항목이 있는 경우 모든 레코드를 반환
### 예시
- `2022년 10월에 작성된 게시글` 제목, 게시글 ID, 댓글 ID, 댓글 작성자 ID, 댓글 내용, 댓글 작성일을 조회하는 SQL문
- 결과는 댓글 작성일을 기준으로 오름차순 정렬
- 댓글 작성일이 같다면 게시글 제목을 기준으로 오름차순 정렬
- `2022년 10월에 작성된 게시글`이므로 board b를 기준으로 where절 작성
```sql
SELECT b.TITLE, b.BOARD_ID, r.REPLY_ID, r.WRITER_ID, r.CONTENTS, date_format(r.CREATED_DATE, '%Y-%m-%d') as CREATED_DATE
from USED_GOODS_BOARD b 
join USED_GOODS_REPLY r on b.BOARD_ID = r.BOARD_ID 
where b.CREATED_DATE like "2022-10%"
order by r.CREATED_DATE, b.TITLE
```


## `ifnull`
- 특정 컬럼의 값이 `null`인지 확인하고 null인 경우 대체 값을 반환
### 예시
- 냉동시설 여부가 `null`인 경우, N으로 출력
```sql
SELECT WAREHOUSE_ID, WAREHOUSE_NAME, ADDRESS, ifnull(FREEZER_YN, 'N')
from FOOD_WAREHOUSE
where ADDRESS like '경기도%'
order by WAREHOUSE_ID
```

## `concat`
- 문자열을 입력한 순서대로 합치는 함수
### 예시
- FISH_INFO 테이블에서 잡은 물고기 중 가장 큰 물고기의 길이를 'cm' 를 붙여 출력하는 SQL 문
```sql
select distinct CONCAT(LENGTH, 'cm') as MAX_LENGTH
from FISH_INFO
where LENGTH = (select max(LENGTH) from FISH_INFO)
```

## `서브쿼리`
- SQL 쿼리 안에 있는 또 다른 쿼리
- 주로 () 괄호로 묶어서 표현
### 예시
- 음식종류별로 즐겨찾기수가 가장 많은 식당의 음식 종류, ID, 식당 이름, 즐겨찾기수를 조회하는 SQL문
- 음식 종류를 기준으로 내림차순 정렬
```sql
SELECT 
    FOOD_TYPE, 
    REST_ID, 
    REST_NAME, 
    FAVORITES
from REST_INFO r1
where FAVORITES = (
    select max(FAVORITES)
    from REST_INFO r2
    where r2.FOOD_TYPE = r1.FOOD_TYPE
)
order by FOOD_TYPE desc
```
1. 서브쿼리: `(SELECT MAX(FAVORITES) FROM REST_INFO r2 WHERE r2.FOOD_TYPE = r1.FOOD_TYPE)`
   <br>
   - 서브쿼리를 사용하여 각 음식 종류별로 최대 즐겨찾기 수 찾기
  
     
3. 메인 쿼리
   <br>
   - 서브쿼리에서 찾은 최대값과 일치하는 레코드 선택
### [프로그래머스 | 헤비 유저가 소유한 장소](https://school.programmers.co.kr/learn/courses/30/lessons/77487)
```sql
SELECT ID, NAME, HOST_ID
from PLACES
where HOST_ID in (
    select HOST_ID
    from PLACES
    group by HOST_ID
    having count(*) >= 2
)
order by ID
```
1. 서브쿼리에서 공간을 2개 등록한 유저 `HOST_ID` 찾기
   - `group by HOST_ID` 호스트ID로 그룹화
   - `having count(*) >= 2` 등록한 공간이 2개 이상인 경우
2. 메인 쿼리
   - `WHERE HOST_ID IN (...)` 서브쿼리에서 찾은 헤비 유저 목록에 있는 레코드 반환
<br>

```sql
SELECT CATEGORY, PRICE as MAX_PRICE, PRODUCT_NAME
from FOOD_PRODUCT
where PRICE in 
(
    select max(PRICE) from FOOD_PRODUCT group by CATEGORY)
    and 
    CATEGORY in ('과자', '국', '김치', '식용유')
    
order by 2 desc
```
1. 서브쿼리
   - '과자', '국', '김치', '식용유' 카테고리 별로 가장 비싼 가격
2. 메인쿼리
   - 서브쿼리에서 찾은 카테고리, 최대 가격, 상품 이름 반환
