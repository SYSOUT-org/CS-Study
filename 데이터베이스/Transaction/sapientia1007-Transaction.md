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

## 📌 트랜잭션 상태
![트랜잭션 상태](https://doooyeon.github.io/assets/img/post/transaction-status.png)
- 활동 (Active) : 트랜잭션이 실행중인 상태
- 실패 (Failed) : 트랜잭션 실행에 오류가 발생하여 중단된 상태
- 철회 (Aborted) : 트랜잭션이 비정상적으로 종료되어 Rollback 연산을 수행한 상태
- 부분 완료 (Partially Committed) : 트랜잭션의 마지막 연산까지 실행했지만, Commit 연산이 실행되기 직전으로 DB에 반영되기 전의 상태
- 완료 (Committed) : 트랜잭션이 성공적으로 종료되어 Commit 연산을 실행한 후의 상태

## 📌 동시성 제어
- 여러 개의 트랜잭션이 실행될 때, 트랜잭션들이 데이터베이스의 <u>일관성</u>을 파괴하지 않고 다른 트랜잭션에 <u>영향</u>을 주지 않으면서 **트랜잭션을 제어하는 것**    
👉🏻 데이터베이스의 공유와 시스템 활용도의 최대화   
👉🏻 데이터베이스의 일관성 유지    
👉🏻 사용자에 대한 응답시간 최소화     

### ✔️ 동시성 문제점
1. **갱신 분실 (lost update)** 
- 두 개의 트랜잭션이 <u>같은 데이터에 대해서 동시에 갱신 작업</u>을 하면 **하나의 갱신 작업이 분실**되는 경우

| 트랜잭션 T1        | 트랜잭션 T2        | 버퍼의 데이터 값 |
|--------------------|--------------------|-----------------|
| A = read_item(X); |                    | X = 1000        |
| A = A - 100;      |                    |                 |
|                    | B = read_item(X); | X = 900         |
|                    | B = B + 100;      |                 |
| write_item(A → X); |                    | X = 900         |
|                    | write_item(B → X); | X = 1100        |

```
- 한 개의 데이터에 두 개의 트랜잭션이 접근하여 갱신하는 작업 ( x = 1000 )

1. X 데이터를 읽어서 -100 처리한 트랜잭션 T1 => x = 900
2. X 데이터를 읽어서 +100 처리한 트랜잭션 T2 => x = 1100
3. 트랜잭션 T1이 A 값을 X에 저장 => x = 900
4. 트랜잭션 T2가 B 값을 X에 저장 => x = 1100

👉🏻 T2가 T1의 갱신 작업을 무효화하고 덧쓰기를 수행한 것으로 갱신 손실 발생
```

2. **모순성 (Inconsistency)** 
- 한 개의 트랜잭션 작업이 *갱신 작업을 하고 있는 상태*에서 <u>또 하나의 트랜잭션이 같은 작업 구역에 침범하여 작업</u>하게 되어 **데이터베이스의 일관성을 해치는 경우**

| 초기값           | A = 1500   | B = 1000   |
|------------------|------------|------------|
|                  | **트랜잭션 T1**             | **트랜잭션 T2**             |
|                  | Read(A)    |     |
|                  | A = A + 300 |    |
|                  | Write(A)   |     |
|                  |     | Read(A)    |
|                  |   | A = A^2    |
|                  |   | Write(A)   |
|                  |            | Read(B)    |
|                  |     | B = B * 3  |
|                  |  | Write(B)   |
|                  | Read(B)    |   |
|                  | B = B + 300 |    |
|                  | Write(B)   |            |
| 결과값           | A = 3600   | B = 3300   |

```
- 이때 T1은 B의 원래값 1000을 사용해 1300이 나올 것을 예상했지만, T2가 갱신한 3000을 사용하여 3300이 나온 모순성이 발생
```

3. **연쇄 복귀 (Cascading Rollback)** 
- 같은 자원을 사용하는 두 개의 트랜잭션 중 *한 개의 트랜잭션이 성공적으로 일을 수행하였다 하더라도 다른 트랜잭션이 처리하는 과정에서 실패*하게 되면 **두 개의 트랜잭션이 모두가 Rollback해야 하는 문제 발생**

| 트랜잭션 T1        | 트랜잭션 T2        | 버퍼의 데이터 값 |
|--------------------|--------------------|-----------------|
| A = read_item(X);  |                    | X = 1000        |
| A = A + 100;       |                    |                 |
| write_item(A → X); |                    | X = 1100        |
|                    | B = read_item(X);  |                 |
|                    | B = B * 3;         |                 |
|                    | write_item(B);     | X = 3300        |
| C = read_item(Y)   |                    | Y = 100         |
| C = C - 50         |                    |                 |
| 실패 => 롤백       |                    |                 |


```
1. 트랜잭션 T1이 X 데이터를 읽어와 처리
2. 트랜잭션 T2이 해당 X 데이터를 읽어와 처리
3. 트랜잭션 T1이 Y 데이터를 읽어와 처리하는 과정에 실패
4. 롤백 과정을 가지려 했으나, T1이 변경한 데이터 X를 읽어와 T2가 처리했기 때문에 해당 과정에서 rollback 불가능
```

4. **비완료 의존성 (Uncommitted Dependency)**
- 한 개의 트랜잭션이 수행 과정에서 실패하였을 때, **이 트랜잭션이 회복되기 전에 다른 트랜잭션이 수행결과를 참조**하는 현상 = Dirty Read

### ✔️ 동시성 제어 해결 방법
- **로킹 (Locking)**
    - 트랜잭션이 어떤 데이터에 접근하고자 할때 로킹을 수행하면, <u>로킹되어 있는 데이터에 다른 트랜잭션이 접근할 수 없음</u>
    - 트랜잭션은 **로킹이 된 데이터에 대해서만 연산을 수행할 수 있으며** 로킹의 단위에는 필드, 레코드, 파일, DB 모두 로킹이 될 수 있음   
    👉🏻 2단계 로킹 규약
        -  Lock과 Unlock 동시에 이루어지면 일관성이 보장되지 않으므로 *Lock만 가능한 단계와 Unlock만 가능한 단계를 구분하여 직렬가능성 보장*
            => 하지만 교착 상태 발생 가능
        - 확장 단계(Growing Phase) : 트랜잭션이 Lock만 할 수 있고, Unlock은 할 수 없음 => 객체를 사용하기 전에 잠금(Lock), 잠금 해제는 수행 불가
        - 축소 단계 (Shrinking Phase) : 트랜잭션이 Unlock만 할 수 있고, Lock은 할수 없음 => 한 번 풀기 시작했으면 (unlock), 더 이상 잠그지 못함

- **타임 스탬프 (Time Stamp)** 
    - 데이터에 접근하는 시간을 미리 정하여 **정해진 시간의 순서대로 데이터에 접근하며 수행**함.
    - 직렬 가능성을 보장하며 시간을 나눠 사용하기 때문에 교착상태가 발생하지 않음 -> 하지만 연쇄 복귀 초래 가능

- **낙관적 병행 제어(Optimistic Concurrency Control)**
    - 트랜잭션 수행 동안은 어떠한 검사도 하지 않고, *트랜잭션 종료 시에 일괄적으로 검사*함 
    - 트랜잭션 수행 동안 그 트랜잭션을 유지되는 데이터 항목의 지역 사본에 대해서만 갱신하며, 트랜잭션 종료 시에 동시성을 위한 트랜잭션 직렬화가 검증되면 일시에 DB로 반영함

- **다중 버전 병행 제어 (Multi-version Concurrency Control)**
    - 하나의 데이터 아이템에 대해 *여러 버전의 값을 유지*하며, 조회 성능을 최대한 유지하기 위한 기법
    - 트랜잭션 간의 충돌 문제는 대기가 아니라 복귀처리 함으로 연쇄 복귀 처리 발생 가능성이 있음

### ✔️ 락 (Lock)
- 갱신 손실 문제를 해결하려면 **상대방 트랜잭션이 데이터를 사용하는지 여부를 알 수 있는 규칙이 필요**함
- 이때 데이터를 수정 중이라는 사실을 알리는 방법의 잠금 장치인 "`락`"을 사용
- <u>`Lock()`을 사용하여 현재 트랜잭션이 해당 데이터를 사용중이니, 사용하고 싶다면 대기해라 라고 알려주는 것</u>

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
SELECT - 공유락 걸지 않음 / UPDATE - 배타락 설정    
**⇒ 다른 트랙잭션에 공유락과 배타락이 걸린 데이터를 대기하지 않고 읽음**
- <u>심지어 다른 트랜잭션이 COMMIT하지 않은 데이터도 읽을 수 있으므로, **오손 데이터 읽기** = SELECT 질의의 대상이 되는 테이블에 대해서 락을 설정하지 않은 것과 같음 (NO LOCK)</u>
- 다른 트랜잭션에서 커밋되지 않은 내용에 접근 가능 **(Dirty Read)**
- `오손 읽기, 반복 불가능 읽기, 유령 데이터 읽기`

### ✔️ **Read Committed**
- 오손 페이지의 참조를 피하기 위해 자신의 데이터를 읽는 동안 공유락을 걸지만, 
트랜잭션이 끝나기 전에라도 해지 가능    
SELECT - 공유락을 걸고 끝나면 바로 해지 / UPDATE - 배타락 설정   
**⇒ 다른 트랜잭션이 설정한 공유락은 읽지만 배타락은 읽지 못함**
- <u>커밋된 내용만 접근 가능
- 한 트랜잭션 내에서 검색 결과가 비일관적인 현상 발생 **(Nonrepeatable read)**</u>
- `반복 불가능 읽기, 유령 데이터 읽기`

### ✔️ **Repeatable Read**
- 자신의 데이터에 설정된 공유락과 배타락을 트랜잭션이 종료할 때까지 유지하여 다른 트랜잭션이 자신의 데이터를 갱신(UPDATE)할 수 없도록 함    
(다른 트랜잭션 데이터는 락 호환성 규칙에 따라 진행 ⇒ 다른 고립화 수준에 비해 데이터의 동시성이 낮아 특별하지 않은 상황이라면 사용하지 않는 것이 좋다)   
SELECT - 공유락을 걸고 트랜잭션을 끝까지 유지 / UPDATE - 배타락 설정
**⇒ 다른 트랜잭션이 설정한 공유락은 읽지만 배타락은 읽지 못함**
- <u>커밋이 완료된 데이터만 읽을 수 있으며, 트랜잭션 범위 내에서 조회한 내용이 항상 동일함을 보장</u>
- **일정범위의 레코드를 두번 이상 읽을 때, 첫 번재 쿼리에서 없던 유령 레코드가 두번째 쿼리에서 나타나는 현상을 Phantom read** `유령 데이터 읽기`

### ✔️ **Serializable**
- 고립 수준이 가장 높은 명령어로, 실행 중인 트랜잭션은 다른 트랜잭션으로부터 완벽하게 분리    
SELECT - 공유락을 걸고 트랜잭션을 끝까지 유지 / UPDATE - 배타락 설정 / 다른 트랜잭션에 설정한 공유락은 읽지만 배타락은 읽지 못함   
    **⇒ 인덱스 공유락을 설정하여 다른 트랜잭션의 INSERT 문이 금지됨**
- <u>한 트랜잭션에서 사용하는 데이터는 다른 트랜잭션이 접근 불가능</u>

## 📌 Spring 사용 코드
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
- 해당 서비스 단을 호출 했을 때, ``price=500``인 경우에는 정상적으로 INSERT
- 하지만 ``pirce=2000``인 예외 상황에서는 INSERT 후 `ROLLBACK`시켜서 DB에 반영이 안되는 것을 확인 가능

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
- 해당 서비스 단을 호출 했을때 ``pirce=2000``인 예외 상황에서는 INSERT 후 ROLLBACK시키지 못하고 예외는 발생하나 DB에 바로 반영됨

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

---

> 출처    
https://dev-coco.tistory.com/72    
https://velog.io/@shasha/Database-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%A0%95%EB%A6%AC     
https://doooyeon.github.io/2018/09/28/transaction.html