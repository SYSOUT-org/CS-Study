# 트랜잭션(Transaction)

트랜잭션은 데이터베이스와 애플리케이션 간의 데이터 거래에서 **안정성을 확보**하기 위한 단일 논리적 작업 단위입니다.  
예를 들어, **계좌 이체** 과정에서 인출 또는 입금만 성공하면 데이터 무결성이 깨질 수 있습니다. 이를 방지하기 위해 트랜잭션을 사용합니다.

트랜잭션은 **모든 작업이 성공적으로 완료되거나, 실패 시 모든 작업이 원래 상태로 되돌아가는 원자성**을 보장하여 데이터의 **일관성과 무결성**을 유지합니다.

---

## 트랜잭션의 필요성: **계좌 이체 예시**

### 문제 상황
- **사용자 A 계좌:** 10,000 원
- **사용자 B 계좌:** 5,000 원
- 사용자 A가 사용자 B에게 2,000원을 이체하는 트랜잭션이 실행됩니다.
- 이 과정에서 A 계좌에서 2,000원 인출이 성공하고, B 계좌에 2,000원 입금이 실패한다면 데이터 무결성이 깨집니다.

### 해결 방법
- 트랜잭션을 통해 **모든 작업이 완료되거나 실패 시 전부 되돌리는** 원자성을 보장하여 문제를 방지할 수 있습니다.

---

## 주요 명령어

### MySQL 명령어
- **`START TRANSACTION`**: 트랜잭션 시작
- **`COMMIT`**: 작업 내용을 데이터베이스에 저장하고 트랜잭션 종료
- **`ROLLBACK`**: 작업(마지막 커밋 전까지)을 취소하고 이전 상태로 되돌림


### Spring Framework
- **`@Transactional`**: 메서드 내의 모든 데이터베이스 작업을 하나의 트랜잭션으로 묶어 처리

---

## ACID 속성

1. **Atomicity (원자성)**
   - 트랜잭션 내 모든 작업이 **모두 성공하거나 모두 취소**되어야 합니다.
   - "All or Nothing"

2. **Consistency (일관성)**
   - 트랜잭션 실행 후 데이터베이스는 항상 **일관성 있는 상태**를 유지해야 합니다.
   - 트랜잭션이 데이터베이스 규칙을 위반하면 `ROLLBACK`됩니다.

3. **Isolation (고립성)**
   - 여러 트랜잭션이 동시에 실행될 때 **서로의 연산에 영향을 주지 않아야 합니다**.
   - DBMS의 동시성 제어는 **Serializability**와 **Recoverability**를 보장합니다.

4. **Durability (영구성)**
   - **커밋된 트랜잭션**의 변경 사항은 시스템 장애가 발생해도 유지됩니다.

---

## 주요 용어 정리

- **R1(A)**: A 데이터를 읽는 작업 (Read)
- **W1(A)**: A 데이터를 쓰는 작업 (Write)
- **operation**:R1(A)와 같은 작업들
- **Schedule**: 여러 트랜잭션이 동시에 실행될 때 연산들의 실행 순서

### Schedule 유형
1. **Serial Schedule**
   - 트랜잭션이 겹치지 않고 순차적으로 실행됩니다.
   - 동시성이 없으므로 성능이 떨어집니다.
   - 현실적으로 사용하기 어렵습니다.
   - **Ex)** A가 B에게 송금하는 트랜잭션과 B가 자신의 통장에 입금하는 트랜잭션의 Serial Schedule:
   - R1(A) – W1(A) – R1(B) – W1(B) – C1 – **R2(B) – W2(B) – C2**


2. **Nonserial Schedule**
   - 트랜잭션이 겹쳐서 실행
   - **동시성**이 높아 같은 시간동안 더 많은 transaction을 처리할 수 있으나, 실행 순서에 따라 이상 결과가 발생할 수 있습니다.
   - R1(A) – W1(A) – R1(B) – **R2(B) – W2(B) – C2** – W1(B) – C1
   - 위와 같은 경우 2번 트랜잭션의 결과가 사라지고 1번 트랜잭션의 결과만 남게 됩니다.
   - Serial schedule 과 동일한 Nonserial schedule을 실행함으로써 Nonserial과 serial의 장점을 모두 챙길 수 있습니다.

### Conflict Operations
- 서로 다른 트랜잭션이 같은 데이터에 접근하며 최소 하나는 `Write` 작업을 수행하는 경우를 말합니다.
- R1(A) – W1(A) – **R2(B) – W2(B) – C2** – R1(B) – W1(B) – C1 
- 위의 경우 R2(B)와 W1(B), W2(B)와 R1(B), W2(B)와 W1(B)가 Conflict operation입니다.


### Conflict Equivalent
- 두 스케줄이 같은 트랜잭션을 포함하고, Conflict Operation의 순서가 동일함을 뜻합니다.
- Ex) 
- A. R1(A) – W1(A) – **R2(B) – W2(B) – C2** – R1(B) – W1(B) – C1
- B. **R2(B) – W2(B) – C2** – R1(A) – W1(A) – R1(B) – W1(B) – C1
- A,B Schedule은 Conflict equivalent합니다. 
- B Schedule은  Serial schedule입니다. 
- 이처럼 Serial schedule과 Conflict equivalent할 때 Conflict serializable하다라고 합니다. 
- 고로 A Schedule은 Conflict serializable 합니다.


- R1(A) – W1(A) – R1(B) – R2(B) – W2(B) – C2 – W1(B) – C1
- 위의 경우는 어떤 Serial schedule과도 Conflict equivalent하지 않습니다.
- 이를 Not conflict serializable 이라고 합니다.


- Conflict serializable한 Nonserial schedule을 실행함으로써 Nonserial과 serial의 장점을 모두 챙길 수 있습니다.
- 어떤 schedule도 serializable하도록 만들어 주는 것이 concurrency control 이고 이것과 밀접하게 관련된 트랜잭션 속성이 Isolation입니다. 
- 하지만 Isolation을 너무 엄격하게 하여 Serializability를 너무 엄격하게 추구하게되면 동시성이 떨어지기 때문에 성능이 줄게됩니다. 
- 이를 개발자들이 필요에따라 유연하게 선택하게 해주는 것이 Isolation level입니다.
- 이에 대해서는 뒤에서 더 설명드리도록 하겠습니다.

---

## Recoverability (회복 가능성)

1. **Unrecoverable Schedule**
   - 한 트랜잭션이 커밋되기 전에 다른 트랜잭션이 데이터를 읽고 커밋하는 경우
   - 이때 첫 번째 트랜잭션이 롤백되면, 두 번째 트랜잭션도 영향을 받아 데이터 불일치가 발생하지만, 이미 커밋되었기 때문에 durability 속성 때문에 롤백이 불가능합니다.

2. **Recoverable Schedule**
   - 읽은 데이터를 작성한 트랜잭션이 먼저 커밋된 후 다른 트랜잭션이 커밋
   - 데이터 일관성을 유지 할 수 있습니다.

3. **Cascading Rollback**
   - 여러 트랜잭션의 롤백이 연쇄적으로 일어나는 것 -> 처리 비용이 많이 듭니다.
   - 데이터를 write한 트랜잭션이 commit 또는 rollback 한 뒤에 데이터를 읽거나 쓰는 schedule만 허용하자!
   - 이를 **cascadeless schedule**이라고 합니다.
   - 하지만 이 경우도 데이터를 읽지 않는 schedule은 다 허용하게 되어서 문제가 발생합니다.
   - 예: `write1(A) – write2(A) – commit2(A) – rollback1(A)`을 하게 되면 결과적으로 1번과 2번 트랜잭션 모두 rollback이 되어버림.
   - 데이터를 write한 트랜잭션이 commit 또는 rollback 한 뒤에 데이터를 읽거나 쓰는 schedule만 허용하자!
   - 이를 **strict schedule**이라고 합니다.
   - recoverable - cascadeless - strict 순으로 범위가 좁아진다고 생각하시면 좋을 것 같습니다.


---

## Isolation Levels (격리 수준)

### 이상 현상
1. **Dirty Read**
   - 커밋되지 않은 데이터를 읽음
2. **Non-repeatable Read**
   - 동일한 데이터를 여러 번 읽을 때 값이 달라짐
3. **Phantom Read**
   - 데이터가 갑자기 추가되거나 제거됨

- 이런 이상한 현상들이 모두 발생하지 않게 만들 수 있지만 그러면 제약사항이 많아져서 동시 처리 가능한 트랜잭션 수가 줄어들어 결국 DB의 전체 처리량(throughput)이 하락하게 됩니다.
- Isolation level : 일부 이상 현상은 허용하는 몇 가지 level을 만들어서 사용자가 필요에 따라서 적절하게 선택할 수 있도록 한 것입니다.

### Isolation Level
| Isolation Level   | Dirty Read | Non-repeatable Read | Phantom Read |
|--------------------|------------|---------------------|--------------|
| Read uncommitted   | Yes        | Yes                 | Yes          |
| Read committed     | No         | Yes                 | Yes          |
| Repeatable read    | No         | No                  | Yes          |
| Serializable       | No         | No                  | No           |
- **Serializable**
   - 이상 현상이 전혀 발생하지 않도록 보장
   - 동시성 감소로 성능 저하 가능
  

- 애플리케이션 설계자는 isolation level을 통해 전체 처리량(throughput)과 데이터 일관성 사이에서 어느 정도 거래(trade)를 할 수 있다
---

## 추가 이상 현상
- **Dirty Write**: 커밋되지 않은 데이터를 덮어씀
- **Lost Update**: 업데이트가 덮어써짐
- **Read Skew** 
- **Write Skew**

---


- MySQL, Oracle, PostgreSQL, SQL Server 등은 표준 Isolation Level에 기반하여 정의하고 있습니다.
- 각 DBMS의 공식 문서를 참고하여 동작 방식을 이해하고 적절히 사용해야 합니다.
- concurrency control은 serializability, recoverability, 그리고 이와 관련된 isolation level을 제공합니다.
- DBMS에서 concurrency control를 구현 하는데에 기본이 되는 락과 MVCC등을 더 공부해보면 좋을 것 같습니다.