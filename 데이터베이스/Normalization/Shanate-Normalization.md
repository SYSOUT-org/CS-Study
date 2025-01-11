# 서론

<aside>
<img src="/icons/heart_purple.svg" alt="/icons/heart_purple.svg" width="40px" />

고객 관리 시스템에서 고객 정보가 여러 테이블에 중복 저장된 상황을 가정해 보겠습니다.

고객의 주소가 변경됐을 때, 모든 테이블에서 해당 고객의 주소를 수정해야 할 것입니다.

이를 한 곳에서만 수정하고 다른 곳에서 수정하지 않는다면? 데이터 불일치 문제가 발생할 것입니다.

</aside>

> 정규화는 이러한 일을 방지하고자, 고객 정보는 하나의 테이블에만 저장하고 다른 테이블은 참조 키를 이용해 고객의 정보를 찾게 만들 수 있습니다.
>

# 정의

<aside>
<img src="/icons/heart_purple.svg" alt="/icons/heart_purple.svg" width="40px" />

정규화는 테이블 간의 데이터 중복을 허용하지 않는 것을 기본 목표로 삼습니다.

1. 중복을 최소화
2. DB 구조 확장 시에 여러 테이블을 수정해야 하는 불상사를 최소화
3. 각종 이상 현상을 방지하기 위함.
</aside>

## 이상 현상(Anomaly)

> 테이블 내의 데이터가 불필요하게 중복돼 발생하는 데이터 불일치 현상을 말합니다.
>

### 삽입 이상(Insertion Anomaly)

- 특정 값이 없는 상태에서 삽입하려고 할 때 발생하는 이상 현상

| student_id | student_name | course_id | course_name |
| --- | --- | --- | --- |
| 1 | Alice | 101 | Math |
| 2 | Bob | 102 | Physics |
| NULL | NULL | 103 | Chemistry |
- Chemistry를 데이터베이스 넣고 싶으나, 학생 값이 없으면 삽입이 불가능합니다.

다음과 같이 정규화를 진행하면 삽입 이상을 방지할 수 있다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/03205dcf-f90c-4bd9-8281-674e7b939609/3dd248ba-ed5d-4b05-aa2b-a43a3ad3d98b/image.png)

### 삭제 이상(Deletion Anomaly)

- 하나의 데이터만을 삭제하려 했으나, 그 데이터가 포함된 행 전체가 삭제되어 정보 손실이 일어나는 현상

### 갱신 이상(Update Anomaly)

- 중복 행(Tuple) 중 일부만 변경해 데이터의 불일치가 발생하는 현상

## 정규화를 통해 얻을 수 있는 장점

- 데이터 중복을 제거함으로써 독립성을 확보한다.
- *입력, 수정, 삭제 성능은 일반적으로 **향상**한다*. 이는 데이터 중복을 제거한 것을 통해 이뤄지는 성능 향상인데, 여러 테이블에 같은 데이터가 저장되어 있다면, 입력, 수정, 삭제가 각 테이블에서 다 이뤄줘야 한다. 그러나 정규화를 이용한다면 단일 테이블에서만 해당 기능을 수행하면 된다.

### 정규화의 종류

![https://en.wikipedia.org/wiki/Database_normalization](https://prod-files-secure.s3.us-west-2.amazonaws.com/03205dcf-f90c-4bd9-8281-674e7b939609/75728a3f-14e0-4f01-a729-89f1145eddde/image.png)

https://en.wikipedia.org/wiki/Database_normalization

정규화 단계에는 순서대로 UNF(비정규화), 1NF, 2NF, 3NF, EKNF(기본 키 정규화), BCNF(보이스-코드 정규화), 4NF, ETNF(필수 행 정규화), 5NF, DKNF(도메인 키 정규화), 6NF가 있으나, 실제로는 제 3 정규화까지 하는 추세이며, SQLD에서는 제 3 정규화까지를, 정보처리기사에서는 1~5 정규화와 BCNF까지를 다룬다.(도부이결다조)

4NF를 넘어서는 정규화는 학문적인 관심사에서 이뤄지는 것이며, 해당 형식 문제가 실제로 나타나는 경우는 매우 드물다.

> 제 3 정규화까지 알고 있으면 되며, 그 이후부터는 학술적인 내용이 다분하다.
>

## 제 1 정규화

- 모든 속성은 반드시 하나의 값을 가진다.

## 제 2 정규화

- PK가 두 개 이상일 때, 그 PK의 부분 집합으로 종속되는 관계가 있다면 분리해야 한다.
- 이를 부분 **함수적 종속 제거**라고 한다.

## 제 3 정규화

- PK가 아닌 일반 칼럼에 의존하는 칼럼이 존재할 시 이를 제거한다.
- A→B, B→C일 때, A→C가 성립하는 것을 의미한다.

## Side Effect

- 반대로, ***조회 성능은 떨어질 수가 있다.*** 정규화는 여러 테이블로 분리하기 때문에 조회 시 복잡성이 증가하기 때문이다.
- 즉, 필요한 정보가 한 테이블에 모두 있는 것이 아니기 때문에 조인 연산이 증가할 것이다. 즉, 각 테이블에서 조건에 맞는 데이터를 계속해서 스캔해야 한다는 것이다.

<aside>
<img src="/icons/heart_purple.svg" alt="/icons/heart_purple.svg" width="40px" />

다음과 같이 주문 관리 조회를 예시로 들어본다.

테이블이 다음과 같이 정규화 되어 있다.

- Customer : 고객 정보 테이블(customer_id, name, email)
- Order : 주문 테이블(order_id, customer_id, order_date)
- OrderDetails : 주문 상세 정보 테이블(order_id, product_id, quantity)
- Product : 제품 정보 테이블(product_id, product_name, price)

**요구사항** : 특정 고객의 주문 내역을 조회하고, 각 주문에 포함된 제품과 그 수량을 알고 싶다.

</aside>

```sql
SELECT c.name AS customer_name, 
       o.order_date, 
       p.product_name, 
       od.quantity
FROM Customer c
JOIN Order o ON c.customer_id = o.customer_id
JOIN OrderDetails od ON o.order_id = od.order_id
JOIN Product p ON od.product_id = p.product_id
WHERE c.customer_id = 123;
```

### 처리과정

1. Customer 테이블에서 customer_id가 123인 고객 정보를 찾는다.
2. Order 테이블과 조인해 해당 고객의 모든 주문 정보를 가져온다.
3. 각 주문과 관련된 주문 상세 정보를 위해 OrderDetails를 조인한다.
4. OrderDetails의 product_id를 이용해 Product 테이블에서 제품 이름과 가격을 가져온다.

# 반정규화

## 반정규화란

- 정규화된 데이터 모델에 대해 성능 향상과 단순화를 위해 데이터를 중복, 통합, 분리하는 기법
- 보통 정규화 시 여러 조인을 요구해 성능이 떨어지기 때문에 의도적으로 정규화를 위배하는 것을 말한다.

### 반정규화 이유

- 조인 연산 과다 : 많은 조인으로 인해 조회 속도가 느려지는 것을 방지.
- 조회 성능 최적화 : 읽기 작업이 쓰기 작업보다 압도적으로 많을 경우.

### 반정규화 주의사항

- 데이터 중복 관리

### Trade-Off

1. 정규화의 이점
- 데이터 중복 제거 → 저장 공간 절약
- 데이터 무결성 유지 → 입력, 수정, 삭제 작업 간소화
- 이상 방지 → 데이터 일관성 유지
1. 조회 성능의 저하
- 조회 시 조인 연산이 많아질 수 있으며 이로 인한 데이터 스캔이 증가
- 복잡한 쿼리 작성 필요

## 실무적 접근

<aside>
<img src="/icons/heart_purple.svg" alt="/icons/heart_purple.svg" width="40px" />

Trade-Off를 생각한다는 것은 곧 실무에서의 요구치를 어떻게 받아들여야 하는 것이다.

그렇기 때문에 정규화와 비정규화의 밸런스를 맞춰야 한다는 것을 의미한다. 정규화는 세밀해질수록 조회 성능이 저하될 가능성이 있는 딜레마가 생겨버리기 때문이다.

</aside>