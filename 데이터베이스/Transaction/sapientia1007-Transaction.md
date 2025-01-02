# 🚩 트랜잭션
- DBMS에서 데이터를 다루는 논리적인 작업 단위
- 데이터베이스의 상태를 변화시키기 위해서 수행하는 작업 단위
    - 이때 데이터베이스 상태를 변화시킨다는 것은 <u>질의어(SQL)을 이용해서 데이터베이스를 접근하는 것</u>을 의미
    ex) ``SELECT``, ``INSERT``, ``DELETE``, ``UPDATE``

<br>

**트랜잭션을 정의하는 이유**  <br>  1. 데이터베이스에서 여러 작업이 동시에 같은 데이터를 다룰 때, **트랜잭션**이 **서로 분리하는 단위**가 됨  
2. 데이터를 다룰 때, **장애가 발생**하면 **트랜잭션**이 **데이터를 복구하는 작업의 단위**가 됨


## 📌 MySQL 트랜잭션 제어 명령어
- ``커밋(Commit)``
    - 모든 부분 작업이 정상적으로 완료하면 **변경사항을 한꺼번에 DB에 반영**
- ``롤백(Rollback)``
    - 부분 작업이 실패하면 트랜잭션 실행 전으로 되돌림  
    -  하나의 트랜잭션이 처리가 비정상적으로 종료되어 <u>데이터베이스의 일관성을 깨뜨렸을 때</u>, 트랜잭션의 일부가 정상적으로 처리되었더라도 트랜잭션의 ``원자성``을 구현하기 위해 **이 트랜잭션이 행한 모든 연산을 취소하는 연산**
    - 일반적으로 ``Rollback``을 명시하면 ``INSERT, DELETE, UPDATE``등의 **작업 전체가 취소**   
    👉🏻 이때 모든 연산을 취소하지 않고 <u>정해진 부분까지만 되돌리고 싶을 때</u> ``SavePoint`` 사용 
        - 전체가 아닌 **특정 부분에서 트랜잭션 취소** 가능


## 📌 트랜잭션의 특징 
- **``Atomicity(원자성)``**   
👉🏻 DBMS는 원자성을 유지하기 위해 회복 관리자 프로그램을 작동시킴
    - 트랜잭션이 원자처럼 더 이상 쪼개지지 않는 **하나의 프로그램 단위로 동작해야 한다**
    - 트랜잭션에 포함된 작업은 **전부 수행**되거나 아니면 **전부 수행되지 않아야 함** (All or Nothing)
- **``Consistency(일관성)``**   
👉🏻 DBMS는 일관성을 유지하기 위해 무결성 제약조건을 활용함
    - 트랜잭션을 수행하기 전이나 수행한 후나 데이터베이스는 **항상 일관된 상태를 유지**해야 함
- **``Isolation(고립성)``**   
👉🏻 DBMS는 고립성을 유지하기 위해 일관성을 유지하는 것과 마찬가지로, 동시성 제어 알고리즘을 작동시킴
    - 수행중인 트랜잭션에 다른 트랜잭션이 끼어들어 **변경 중인 데이터 값을 훼손하는 일이 없어야 함**
    - 데이터베이스는 공유가 목적이므로, *여러 트랜잭션이 동시에 수행*되는데, 이때 **같은 데이터를 동시에 읽고 쓸 경우** 변경중인 데이터를 다른 트랜잭션이 사용하게 되면 **일관성이 훼손** ➡️ 이때 동시에 사용하지 못하게 해야하는 **동시성 제어**가 필요
- **``Durability(영구성)``**   
👉🏻 DBMS는 지속성을 유지하기 위해 회복 관리자 프로그램을 이용함
    - 수행을 성공적으로 완료한 트랜잭션은 **변경한 데이터를 영구히 저장해야 함**
    - 트랜잭션이 정상적으로 완료(commit) 혹은 부분완료(partial commit)한 데이터는 DBMS가 책임지고, 데이터베이스에 로그 파일을 기록되어야 함 ➡️ 이게 지속성

## 📌 트랜잭션 격리 수준 

| 고립 수준         | 문제            | 오손 읽기       | 반복불가능 읽기 | 유령 데이터 읽기 |
|-------------------|----------------|----------------|----------------|----------------|
| READ UNCOMMITTED | (level = 0)    | 발생 가능      | 발생 가능      | 발생 가능      |
| READ COMMITTED   | (level = 1)    | 불가능         | 발생 가능      | 발생 가능      |
| REPEATABLE READ  | (level = 2)    | 불가능         | 불가능         | 발생 가능      |
| SERIALIZABLE     | (level = 3)    | 불가능         | 불가능         | 불가능         |

> **오손 쓰기 (Dirty Write)** 
: 같은 데이터에 대해 동시에 두 개 이상의 트랜잭션이 값을 바꾸고자 할 때 발생되는 현상

> **오손 읽기 (Dirty read)** = 비완료 의존성 (Uncommitted Dependency) 
: 아직 종료(commit)되지 않은 트랜잭션의 쓰기 내용을 읽는 것으로 비정상적 상태의 데이터를 읽게 되는 현상
ex) 읽기 작업을 하는 트랜잭션 T1이 쓰기 작업을 하는 트랜잭션 T2가 작업한 중간에 데이터를 읽으므로 발생 → 작업 중인 트랜잭션 T2가 작업을 철회할 경우,  트랜잭션 T1은 무효가 된 데이터를 읽게 되고 잘못된 결과를 도출

> **반복 불가능 읽기 (Non-Repeatable Read)** : 어떤 트랜잭션에서 동일한 데이터의 값을 매번 읽을 때 마다 틀려지는 현상
ex) 트랜잭션 T1이 데이터를 읽고 작업하던 중 트랜잭션 T2가 데이터를 변경함 → 이때 T1은 변경한 데이터를 보고 다시 한 번 작업을 함 → T1 입장에서는 같은 SQL문이 다른 결과를 도출함

> **유령 읽기 (Phantom Read)** : 기존 데이터는 동일한데 새로 추가된 값에 의해 데이터 값이 변경되는 현상 
ex) 트랜잭션 T1이 데이터를 읽고 나서 트랜잭션 T2가 데이터를 새로 삽입하면, 트랜잭션 T1이 다시 한번 데이터를 읽을때 이전에 없던 데이터가 나타남

### ✔️ **Read Uncommitted**
- 고립 수준이 가장 낮은 명령어로, 자신의 데이터에 아무런 공유 락을 걸지 않음  
*(배타락은 갱신 손실 문제 때문에 걸어야 함)*   
SELECT - 공유락 걸지 않음 / UPDATE - 배타락  
**⇒ 다른 트랙잭션에 공유락과 배타락이 걸린 데이터를 대기하지 않고 읽음**
- 심지어 다른 트랜잭션이 COMMIT하지 않은 데이터도 읽을 수 있으므로, **오손 데이터 읽기** = SELECT 질의의 대상이 되는 테이블에 대해서 락을 설정하지 않은 것과 같음 (NO LOCK)
- 다른 트랜잭션에서 커밋되지 않은 내용에 접근 가능 **(Dirty Read)**
- `오손 읽기, 반복 불가능 읽기, 유령 데이터 읽기`

### ✔️ **Read Committed**
- 오손 페이지의 참조를 피하기 위해 자신의 데이터를 읽는 동안 공유락을 걸지만, 
트랜잭션이 끝나기 전에라도 해지 가능 
SELECT - 공유락을 걸고 끝나면 바로 해지 / UPDATE - 배타락 설정
**⇒ 다른 트랜잭션이 설정한 공유락은 읽지만 배타락은 읽지 못함**
- 커밋된 내용만 접근 가능
- 한 트랜잭션 내에서 검색 결과가 비일관적인 현상 발생 **(Nonrepeatable read)**
- `반복 불가능 읽기, 유령 데이터 읽기`

### ✔️ **Repeatable Read**
- 자신의 데이터에 설정된 공유락과 배타락을 트랜잭션이 종료할 때까지 유지하여 다른 트랜잭션이 자신의 데이터를 갱신(UPDATE)할 수 없도록 함 
(다른 트랜잭션 데이터는 락 호환성 규칙에 따라 진행 ⇒ 다른 고립화 수준에 비해 데이터의 동시성이 낮아 특별하지 않은 상황이라면 사용하지 않는 것이 좋다)
SELECT - 공유락을 걸고 트랜잭션을 끝까지 유지 / UPDATE - 배타락 설정
**⇒ 다른 트랜잭션이 설정한 공유락은 읽지만 배타락은 읽지 못함**
- 커밋이 완료된 데이터만 읽을 수 있으며, 트랜잭션 범위 내에서 조회한 내용이 항상 동일함을 보장
- **일정범위의 레코드를 두번 이상 읽을 때, 첫 번재 쿼리에서 없던 유령 레코드가 두번째 쿼리에서 나타나는 현상을 Phantom read** `유령 데이터 읽기`

### ✔️ **Serializable**
- 고립 수준이 가장 높은 명령어로, 실행 중인 트랜잭션은 다른 트랜잭션으로부터 완벽하게 분리
SELECT - 공유락을 걸고 트랜잭션을 끝까지 유지 / UPDATE - 배타락 설정 / 다른 트랜잭션에 설정한 공유락은 읽지만 배타락은 읽지 못함 
    **⇒ 인덱스 공유락을 설정하여 다른 트랜잭션의 INSERT 문이 금지됨**
- 한 트랜잭션에서 사용하는 데이터는 다른 트랜잭션이 접근 불가능

## 📌 사용 코드
👉🏻 **Transactional 사용한 INSERT**
```java
    @Transactional
    public void createProduct(String name, Long price) {
        Product product = Product.builder()
                .name(name)
                .price(price)
                .build();

        productJpaRepository.save(product);

        if (price > 1000) {
            throw new RuntimeException("Price Exception - Rollback");
        }
    }
```
- 해당 서비스 단을 호출 했을 때, ``price=500``인 경우에는 정상적으로 Insert
- 하지만 ``pirce=2000``인 예외 상황에서는 Insert 후 Rollback시켜서 DB에 반영이 안되는 것을 확인 가능

```
Hibernate: insert into product (name,price) values (?,?)
Successfully Saved 
Hibernate: insert into product (name,price) values (?,?)
Price Exception - Rollback
```

👉🏻 **Transactional 사용하지 않은 INSERT**
```java
    public void createProductByNonTransaction(String name, Long price) {
        Product product = Product.builder()
                .name(name)
                .price(price)
                .build();

        productJpaRepository.save(product);

        if (price > 1000) {
            throw new RuntimeException("Price Exception - Rollback");
        }
    }
```
- 해당 서비스 단을 호출 했을때 ``pirce=2000``인 예외 상황에서는 Insert 후 Rollback시켜키지 못하고 DB에 바로 반영됨

```
Hibernate: insert into product (name,price) values (?,?)
2025-01-02T13:50:07.947+09:00 ERROR 26280 --- [nio-8080-exec-3] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: java.lang.RuntimeException: Price Exception - Rollback] with root cause

java.lang.RuntimeException: Price Exception - Rollback
```

👉🏻 **Transactional(readOnly=true)인 경우, 쓰기 시도**
```java
    @Transactional(readOnly = true)
    public ProductResp readProduct(Long id) {
        return ProductResp.builder()
                .name(productJpaRepository.findById(id).orElseGet(null).getName())
                .build();
    }

    @Transactional(readOnly = true)
    public void readProductWithInsert() {
        Product product = Product.builder()
                .name("test")
                .price(200L)
                .build();

        productJpaRepository.save(product);
    }
```
- 해당 서비스 단에서 readProduct를 호출하면, 정상적으로 데이터를 SELECT
- 하지만, readProductWithInsert를 호출하면, readOnly인 Transaction에서 INSERT를 시도하므로 예외 발생 
```
Hibernate: select p1_0.id,p1_0.name,p1_0.price from product p1_0 where p1_0.id=?
Hibernate: insert into product (name,price) values (?,?)
2025-01-02T13:57:00.942+09:00  WARN 18816 --- [nio-8080-exec-1] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 0, SQLState: S1009
2025-01-02T13:57:00.943+09:00 ERROR 18816 --- [nio-8080-exec-1] o.h.engine.jdbc.spi.SqlExceptionHelper   : Connection is read-only. Queries leading to data modification are not allowed
2025-01-02T13:57:00.958+09:00 ERROR 18816 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.orm.jpa.JpaSystemException: could not execute statement [Connection is read-only. Queries leading to data modification are not allowed] [insert into product (name,price) values (?,?)]] with root cause

java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed
```