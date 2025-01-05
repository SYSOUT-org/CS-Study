# 🚩 트랜잭션
- DBMS에서 데이터를 다루는 논리적인 작업 단위
- 데이터베이스의 상태를 변화시키기 위해서 수행하는 작업 단위
    - 이때 데이터베이스 상태를 변화시킨다는 것은 <u>질의어(SQL)을 이용해서 데이터베이스를 접근하는 것</u>을 의미
    ex) ``SELECT``, ``INSERT``, ``DELETE``, ``UPDATE``

<br>

**트랜잭션을 정의하는 이유**  <br>  1. 데이터 동시 접근 시 **충돌 방지** : 여러 작업이 같은 데이터를 다룰 때 **분리된 작업 단위**로 관리       
ex) 두 사용자가 같은 상품 재고를 동시에 수정하려고 할 때, 한 사용자의 작업이 완료된 후에 다른 사용자의 작업이 처리되도록 보장   
<br>
2. 장애 발생 시 데이터 **복구 용이** : **트랜잭션 단위**로 복구 가능   
ex) 은행 계좌 이체 중 시스템 장애가 발생하면, 모든 작업을 취소하여 원래 상태로 복구

## 📌 MySQL 트랜잭션 제어 명령어
- ``커밋(Commit)``
    - 모든 부분 작업이 정상적으로 완료하면 **변경사항을 한꺼번에 DB에 반영**
- ``롤백(Rollback)``
    - 작업 중 일부가 실패하면 트랜잭션 실행 전 상태로 되돌림
    -  하나의 트랜잭션이 처리가 비정상적으로 종료되어 <u>데이터베이스의 일관성을 깨뜨렸을 때</u>, 트랜잭션의 일부가 정상적으로 처리되었더라도 트랜잭션의 ``원자성``을 구현하기 위해 **이 트랜잭션이 행한 모든 연산을 취소하는 연산**
    - 일반적으로 ``Rollback``을 명시하면 ``INSERT, DELETE, UPDATE``등의 **작업 전체가 취소**   
    👉🏻 이때 모든 연산을 취소하지 않고 <u>정해진 부분까지만 되돌리고 싶을 때</u> ``SavePoint`` 사용 

## 📌 트랜잭션의 특징 
- **``Atomicity(원자성)``**   
👉🏻 DBMS는 원자성을 유지하기 위해 회복 관리자 프로그램을 작동시킴
    - 트랜잭션이 원자처럼 더 이상 쪼개지지 않는 **하나의 프로그램 단위로 동작해야 한다**
    - 트랜잭션에 포함된 작업은 **전부 수행**되거나 아니면 **전부 수행되지 않아야 함** (All or Nothing)
    - ex) 계좌 이체 시, A 계좌에서 금액을 빼는 작업과 B 계좌로 금액을 더하는 작업이 모두 완료되거나 모두 취소되어야 함
- **``Consistency(일관성)``**   
👉🏻 DBMS는 일관성을 유지하기 위해 무결성 제약조건을 활용함
    - 트랜잭션을 수행하기 전이나 수행한 후나 데이터베이스는 **항상 일관된 상태를 유지**해야 함
- **``Isolation(고립성)``**   
👉🏻 DBMS는 고립성을 유지하기 위해 일관성을 유지하는 것과 마찬가지로, 동시성 제어 알고리즘을 작동시킴
    - 수행중인 트랜잭션에 다른 트랜잭션이 끼어들어 **변경 중인 데이터 값을 훼손하는 일이 없어야 함**
    - 데이터베이스는 공유가 목적이므로, *여러 트랜잭션이 동시에 수행*되는데, 이때 **같은 데이터를 동시에 읽고 쓸 경우** 변경중인 데이터를 다른 트랜잭션이 사용하게 되면 **일관성이 훼손** ➡️ 이때 동시에 사용하지 못하게 해야하는 **동시성 제어**가 필요    
    ex) 두 사용자가 동시에 같은 데이터를 수정하려 할 때, 한 사용자의 작업이 끝난 후 다른 사용자가 데이터를 수정할 수 있음
- **``Durability(영구성)``**   
👉🏻 DBMS는 지속성을 유지하기 위해 회복 관리자 프로그램을 이용함
    - 수행을 성공적으로 완료한 트랜잭션은 **변경한 데이터를 영구히 저장해야 함**
    - 트랜잭션이 정상적으로 완료(commit) 혹은 부분완료(partial commit)한 데이터는 DBMS가 책임지고, 데이터베이스에 로그 파일을 기록되어야 함 ➡️ 이게 지속성       
    ex) 주문 완료 후 시스템이 다운되더라도 주문 내역은 데이터베이스에 남아 있어야 함

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

| 트랜잭션 T1         | 트랜잭션 T2        | 버퍼의 데이터 값 |
|--------------------|--------------------|----------------|
| A = read_item(X);  |                    | X = 1000       |
| A = A - 100;       |                    |                |
|                    | B = read_item(X); | X = 900         |
|                    | B = B + 100;      |                 |
| write_item(A → X); |                   | X = 900         |
|                    | write_item(B → X);| X = 1100        |

```
- 한 개의 데이터에 두 개의 트랜잭션이 접근하여 갱신하는 작업 ( x = 1000 )

1. X 데이터를 읽어서 -100 처리한 트랜잭션 T1 => x = 900
2. X 데이터를 읽어서 +100 처리한 트랜잭션 T2 => x = 1100
3. 트랜잭션 T1이 A 값을 X에 저장 => x = 900
4. 트랜잭션 T2가 B 값을 X에 저장 => x = 1100

👉🏻 T2가 T1의 갱신 작업(x=900)을 무효화하고 덧쓰기를 수행(x=1100)한 것으로 갱신 손실 발생
```

2. **모순성 (Inconsistency)** 
- 한 개의 트랜잭션 작업이 *갱신 작업을 하고 있는 상태*에서 <u>또 하나의 트랜잭션이 같은 작업 구역에 침범하여 작업</u>하게 되어 **데이터베이스의 일관성을 해치는 경우**

| 초기값           | A = 1500         | B = 1000          |
|------------------|------------------|-------------------|
|                  | **트랜잭션 T1** | **트랜잭션 T2**    |
|                  | Read(A)         |                   |
|                  | A = A + 300     |                   |
|                  | Write(A)        |                   |
|                  |                 | Read(A)           |
|                  |                 | A = A^2           |
|                  |                 | Write(A)          |
|                  |                 | Read(B)           |
|                  |                 | B = B * 3         |
|                  |                 | Write(B)          |
|                  | Read(B)         |                   |
|                  | B = B + 300     |                   |
|                  | Write(B)        |                   |
| 결과값           | A = 3600         | B = 3300          |

```
- 이때 T1은 B의 원래값 1000을 사용해 1300이 나올 것을 예상했지만, T2가 갱신한 3000을 사용하여 3300이 나온 모순성이 발생
```

3. **연쇄 복귀 (Cascading Rollback)** 
- 같은 자원을 사용하는 두 개의 트랜잭션 중 *한 개의 트랜잭션이 성공적으로 일을 수행하였다 하더라도 다른 트랜잭션이 처리하는 과정에서 실패*하게 되면 **두 개의 트랜잭션이 모두가 Rollback해야 하는 문제 발생**

| 트랜잭션 T1        | 트랜잭션 T2              | 버퍼의 데이터 값 |
|--------------------|-------------------------|-----------------|
| A = read_item(X);  |                         | X = 1000        |
| A = A + 100;       |                         |                 |
| write_item(A → X); |                         | X = 1100        |
|                    | B = read_item(X);       |                 |
|                    | B = B * 3;              |                 |
|                    | write_item(B → X);      | X = 3300        |
| C = read_item(Y)   |                         | Y = 100         |
| C = C - 50         |                         |                 |
| 실패 => 롤백        |                         |                 |


```
1. 트랜잭션 T1이 X 데이터를 읽어와 처리
2. 트랜잭션 T2이 해당 X 데이터를 읽어와 처리
3. 트랜잭션 T1이 Y 데이터를 읽어와 처리하는 과정에 실패
4. 롤백 과정을 가지려 했으나, T1이 변경한 데이터 X를 읽어와 T2가 처리했기 때문에 해당 과정에서 rollback 불가능
```

4. **비완료 의존성 (Uncommitted Dependency)**
- 한 개의 트랜잭션이 수행 과정에서 실패하였을 때, **이 트랜잭션이 회복되기 전에 다른 트랜잭션이 수행결과를 참조**하는 현상 = Dirty Read

### ✔️ 동시성 제어 해결 방법
- **로킹 (Locking)** : 데이터 접근 시 락을 설정하여 동시성 문제 방지      
👉🏻 `Lock()`을 사용하여 현재 트랜잭션이 해당 데이터를 사용중이니, 사용하고 싶다면 대기해라 라고 알려주는 것</u>
    - 공유락(Shared Lock) : 다른 트랜잭션이 데이터를 읽고 있는 동안 수정 차단 (SELECT ⭕ WRITE ❌)     
    - 배타락(Exclusive Lock) : 한 트랜젝션만 데이터에 접근 가능, 해당 트랜잭션이 데이터를 수정하는 동안 다른 트랜잭션은 읽기/쓰기 불가 (SELECT ❌ WRITE ❌)     
    ➡️ 하지만 교착 상태 발생 가능
- **타임 스탬프 (Time Stamp)** : 트랜잭션 순서를 시간 기준으로 정해 직렬성 보장      
👉🏻 직렬 가능성을 보장하며, 고유한 타임 스탬프를 할당하기 때문에 교착상태가 발생하지 않음 ➡️ 하지만 연쇄 복귀 초래 가능
- **낙관적 병행 제어 (Optimistic Concurrency Control)** : 트랜잭션 종료 시 동시성 검사를 통해 충돌 여부 확인     
👉🏻 트랜잭션이 다른 트랜잭션과 충돌할 가능성이 적다고 가정하고, 만약 충돌이 발생 시 그에 맞춰 복구
- **다중 버전 병행 제어 (Multi-version Concurrency Control = MVCC)**
 : 데이터 버전을 관리해 조회 성능 극대화

## 📌 트랜잭션 격리 수준 

| 고립 수준         | 문제            | 오손 읽기       | 반복불가능 읽기 | 유령 데이터 읽기 |
|------------------|-----------------|----------------|----------------|----------------|
| READ UNCOMMITTED | (level = 0)     | 발생 가능       | 발생 가능       | 발생 가능      |
| READ COMMITTED   | (level = 1)     | 불가능          | 발생 가능       | 발생 가능      |
| REPEATABLE READ  | (level = 2)     | 불가능          | 불가능          | 발생 가능      |
| SERIALIZABLE     | (level = 3)     | 불가능          | 불가능          | 불가능         |

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
- 가장 낮은 격리 수준으로, 다른 트랜잭션이 커밋하지 않은 데이터를 읽을 수 있음 = `Dirty Read` 발생
- SELECT 문에서 공유 락을 걸지 않으므로 데이터의 일관성이 깨질 가능성이 높음    
*(UPDATE 배타락은 갱신 손실 문제 때문에 걸어야 함)*       
- `오손 읽기(Dirty Read)`, `반복 불가능 읽기(Non-Repeatable Read)`, `유령 읽기(Phantom Read)` 모두 발생 가능     

### ✔️ **Read Committed**
- 커밋된 데이터만 읽을 수 있음 = `오손 읽기(Dirty Read)` 방지
- SELECT 시 공유 락을 걸지만, 트랜잭션이 끝나기 전에 해제되어 반복 불가능 읽기 발생 가능     
*(UPDATE 배타락은 갱신 손실 문제 때문에 걸어야 함)*       
- `반복 불가능 읽기(Non-Repeatable Read)`, `유령 읽기(Phantom Read)`는 여전히 발생 가능

### ✔️ **Repeatable Read**
- 커밋이 완료된 데이터만 읽을 수 있으며, 동일한 트랜잭션 내에서 같은 데이터를 여러 번 조회할 때 항상 동일한 결과를 보장 = `반복 불가능 읽기(Non-Repeatable Read)`방지
- SELECT 시 공유 락을 걸고, 트랜잭션 종료 시까지 유지     
*(UPDATE 배타락은 갱신 손실 문제 때문에 걸어야 함)*       
- `유령 읽기(Phantom Read)`는 여전히 발생 가능

### ✔️ **Serializable**
- 가장 높은 격리 수준으로, 모든 트랜잭션이 순차적으로 실행되는 것처럼 동작
- 실행 중인 트랜잭션은 다른 트랜잭션으로부터 완벽하게 분리     
= 한 트랜잭션에서 사용하는 데이터는 다른 트랜잭션이 접근 불가능
- SELECT 시 인덱스 공유 락을 설정하여 다른 트랜잭션의 INSERT/UPDATE/DELETE를 금지

## 📌 트랜잭션 회복
- 데이터베이스에 장애가 발생했을 때 데이터베이스를 원래 상태(일관성이 있는 상태)로 되돌리는 기능      
👉🏻 하드웨어 오동작, 소프트웨어의 손상, 교착 상태 등에 의해 트랜잭션의 연속적인 수행에 장애를 주는 **시스템 장애** 현상     
👉🏻 저장장치인 디스크의 블록의 손상이나 디스크 헤드 등의 충돌 등에 의해 데이터베이스의 일부 또는 전부가 물리적으로 손상된 **미디어 장애** 현상      
👉🏻 데이터베이스에 접근하는 소프트웨어의 논리적인 오류로 트랜잭션의 수행이 실패하는 **트랜잭션 장애** 현상    

### ✔️ 메모리 덤프 방법
- 주기적으로 데이터베이스에 중요도에 기반하여 전체를 복사해 두는것
### ✔️ 그림자 페이징(Shadow Paging) 방법
- 메모리 덤프와 비슷하지만, 페이지 단위로 복사해 두는 것 
### ✔️ 로그(Log) 방법
> 로그의 타입 : `START`, `INSERT`, `UPDATE`, `DELETE`, `ABORT`, `COMMIT`
- 트랜잭션 내용을 기록하는 별도의 파일로 저장해서 사용   
- 트랜잭션이 수행 중이거나 수행이 종료된 후 발생하는 **데이터베이스 손실을 방지**하기 위해    
<u>트랜잭션의 데이터베이스 기록을 추적하는 로그 파일</u>을 사용    
    - 로그 파일의 역할 : 트랜잭션 변경 내용을 데이터베이스에 반영하기 전, 로그 파일에 미리 기록하여 복구 및 추적에 사용됩니다.

#### **🔍 로그 파일의 회복 방법** <br>
1. 장애 발생하여 시스템 재가동 시, DBMS는 로그파일을 먼저 살펴봄
2. DBMS는 트랜잭션이 **종료**되었는지 혹은 **중단**되었는지 여부 판단
3. `종료`된 트랜잭션은 종료를 확정하기 위해 **재실행(RRED)** 진행     
`중단`된 트랜잭션은 없던일로 되돌리기 위해 **취소(UNDO)**  진행
    -  **`REDO`** : 트랜잭션에 의해 변경된 모든 데이터 항목들을 로그 파일에 있는 변경 이후 값(new value)로 대체     
        - 장애 발생 후, 시스템 재가동 시 로그파일에 트랜잭션 `START`, `COMMIT`이 있는 경우
        - 트랜잭션이 모두 완료되었다는 의미이지만, 변경 내용이 DB에 반영이 안됨    
        👉🏻 <u>트랜잭션을 다시 DB에 저장하는 과정</u>
        ```
        START T1
        UPDATE T1: A=10 -> A=20
        COMMIT T1
        ```
        > 위 예시에서는 START와 COMMIT이 모두 있는 경우로, 장애가 발생해 A=20이 DB에 반영되지 않았다면 A=20으로 반영하기 위해 **REDO**
    - **`UNDO`** : 트랜잭션에 의해 변경된 모든 데이터 항목들을 로그 파일에 있는 변경 이전 값(old value)로 대체
        - 장애 발생 후, 시스템 재가동시 로그파일에 트랜잭션 `START`있고 `COMMIT`이 없는 경우
        - 트랜잭션이 완료 되지 못한 것으로, 트랜잭션이 한 일을 모두 취소해야 함      
        👉🏻 <u>변경 내용이 DB에 반영되어 있을 수 있기 때문에, 로그를 보면서 변경한 내용을 DB에서 원상 복구 해야함</u> 
        ```
        START T2
        UPDATE T2: B=5 -> B=15
        ABORT T2
        ```
        > 위 예시에서는 START는 있고 COMMIT이 없는 경우로, 장애가 발생해 B=15가 DB에 반영되었더라도 복구를 하기 위해 **UNDO**
- **즉시 갱신 (Immeditate Update)**
    - 갱신데이터 → 로그, 버퍼 → DB 작업이 동시
    - 데이터 갱신이 이루어지면 즉시 DB에 반영
    - 부분완료가 되면, 갱신 데이터는 로그에 기록이 끝남
    - 쿼리 하나당 바로바로, COMMIT이 없어도 로그 저장    
    👉🏻 트랜잭션이 데이터를 갱신하면 트랜잭션이 부분 완료되기 전이라도, 즉시 실제 DB에에
반영하는 방법
- **지연 갱신 (Deferred Update)**
    - 트랜잭션이 성공적으로 완료될 때까지 DB에 대한 실질적인 갱신을 연기하는 방법
    - 갱신데이터 → 로그가 끝난 후 부분완료를 하고 버퍼 → DB 작업이 진행되는 방법
    - 트랜잭션이 다 끝난 후, 로그에 저장 정보를 DB에 일괄 적용
    - COMMIT이 있어야만 로그 저장

👉🏻 **체크 포인트를 이용한 회복** <br>
: 로그를 기록하면서, 회복 관리자가 일정한 시간 간격으로 검사 시점을 생성하는 것   
➡️ 회복 시 많은 양의 로그를 검색하고 갱신하는 시간을 줄일 수 있음

> ✅ 체크포인트 **이전**에 `COMMIT 기록이 있는` 경우    
이미 변경 내용이 데이터베이스에 모두 기록된 후이기 때문에, 아무 작업이 필요 없음

> ✅ 체크 포인트 **이후**에 `COMMIT 기록이 있는` 경우     
체크 포인트 이후의 변경 내용이 데이터베이스에 반영되지 않았으므로 **REDO를 진행**

> ✅ 체크 포인트 **이후**에 `COMMIT 기록이 없는` 경우    
**즉시 갱신 방법**을 사용했다면 `버퍼의 내용`이 반영됐을 수도 있기 때문에, 원상복구를 위해 **UNDO를 진행**    
**지연 갱신 방법**을 사용했다면 COMMIT 이전의 버퍼 내용을 데이터베이스에 반영하지 않기 때문에 **아무 작업이 필요 없음**


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
https://s-y-130.tistory.com/232    
MYSQL로 배우는 데이터베이스 개론과 실습