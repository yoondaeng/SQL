### `group by`

- 같은 값을 가진 행끼리 하나의 그룹으로 뭉쳐줌
- 보통 집계함수와 같이 사용
    - COUNT() : 행의 개수
    - AVG() : 행 안에 있는 값의 평
    - MIN() : 행 안에 있는 값의 최솟
    - MAX() : 행 안에 있는 값의 최댓값
    - SUM() : 행 안에 있는 값의 합

### `having`

- `where`은 집계함수와 함께 사용할 수 없으므로 `having` 을 사용

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5;
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
    

## ✨Date → Str 형 변환

---

### **`DATE_FORMAT(날짜, 출력 형식)`**

```sql
SELECT DATE_FORMAT('2019-09-16 20:23:12', '%Y-%m-%d')
```

### **`DATADIFF(날짜1, 날짜2)`**

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
