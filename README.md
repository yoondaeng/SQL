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
    

### **`distinct`**

- 테이블 열에서 고유한 값만 반환

```sql
SELECT DISTINCT Country FROM Customers;
```

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
