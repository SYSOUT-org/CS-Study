# 트랜잭션(Transaction) 기초 정리

트랜잭션은 데이터베이스의 상태를 변화시키는 작업의 **논리적 단위**로, 여러 작업을 묶어서 하나의 작업처럼 처리한다. 이를 통해 데이터의 **일관성**과 **무결성**을 보장한다.  
**→ 쉽게 말해**: 데이터베이스 세계의 작업 단위. 게시글 수정, 만 원 송금, 재고 수량 확인 같은 이벤트에서 발생하는 데이터베이스 작업들을 묶어서 '트랜잭션'이라고 한다.

---

## 트랜잭션의 주요 특징 (ACID)
트랜잭션은 다음 **4가지 특성**을 만족해야 한다.

### 1. **Atomicity (원자성)**
- 트랜잭션은 **모두 성공하거나, 모두 실패**해야 함.
- 중간에 문제가 발생하면 **Rollback**으로 작업을 원상태로 복구.

### 2. **Consistency (일관성)**
- 트랜잭션 완료 후, 데이터는 항상 **일관된 상태**로 유지됨.
- 비즈니스 규칙이나 데이터 무결성을 위반하지 않아야 함.

### 3. **Isolation (고립성)**
- 여러 트랜잭션이 동시에 실행될 때, 서로 간섭하지 않아야 함.
- 이를 위해 **격리 수준(Isolation Level)**이 필요.

### 4. **Durability (지속성)**
- 트랜잭션이 성공적으로 완료되면, 그 결과는 **영구적으로 저장**.
- 장애가 발생해도 데이터는 손실되지 않아야 함.

---

## ACID 각 특성의 위반 사례와 해결 방안

### 1. **Atomicity (원자성)**  
#### 위반 사례  
- **상황**: 은행에서 A 계좌에서 B 계좌로 100만 원 송금 중,  
  - A 계좌에서 100만 원이 차감되었으나, B 계좌 입금 전에 오류 발생.  
  - 결과: A 계좌의 돈은 사라지고, B는 돈을 받지 못함.

#### 해결 방안  
- **Rollback**을 통해 A 계좌의 100만 원 차감 작업을 되돌림.

---

### 2. **Consistency (일관성)**  
#### 위반 사례  
- **상황**: 상품 재고 시스템에서 재고 수량이 음수로 저장됨.  
  - 재고 10개인 상품에 대해 15개 주문 가능하게 설정.  
  - 결과: 불가능한 음수 재고 상태 발생.

#### 해결 방안  
- "재고는 0 미만이 될 수 없다" 같은 제약 조건 설정.  
- 트랜잭션 실행 전후 데이터의 일관성 검증.

---

### 3. **Isolation (고립성)**  
#### 위반 사례 1 (Dirty Read)  
- **상황**: 트랜잭션 A가 상품 가격 수정 중, 트랜잭션 B가 커밋되지 않은 데이터를 읽음.  
  - 트랜잭션 A: 10,000원 → 8,000원으로 수정 중 (미완료).  
  - 트랜잭션 B: 8,000원을 읽어 계산.  
  - 이후 A가 실패 후 Rollback되면, B는 잘못된 데이터를 사용하게 됨.

#### 위반 사례 2 (Non-repeatable Read)  
- **상황**: 트랜잭션 A가 같은 데이터를 여러 번 읽을 때 값이 달라짐.  
  - 트랜잭션 A: 재고 10개 조회.  
  - 트랜잭션 B: 재고를 5개로 변경 후 커밋.  
  - 트랜잭션 A가 재조회 시, 재고는 5개로 변경됨.

#### 해결 방안  
- 격리 수준 설정으로 Dirty Read와 Non-repeatable Read 방지.  
- 중요한 경우 **Serializable**로 설정.

---

### 4. **Durability (지속성)**  
#### 위반 사례  
- **상황**: 결제 완료 후 시스템 장애로 주문 내역이 데이터베이스에 저장되지 않음.  
  - 사용자: 결제가 완료되었지만 주문이 누락된 상태.

#### 해결 방안  
- 트랜잭션 커밋 후 결과를 안정적인 저장소에 기록.  
- 장애 복구 시스템 구축.

---

## 트랜잭션 상태
트랜잭션은 다음 5가지 상태로 진행된다.

1. **Active (활성)**: 트랜잭션 실행 중.  
2. **Partially Committed (부분 완료)**: 명령어가 실행 완료.  
3. **Committed (완료)**: 트랜잭션 성공, 데이터베이스 반영.  
4. **Failed (실패)**: 오류 발생, 작업 취소 필요.  
5. **Aborted (중단)**: Rollback 후 이전 상태로 복구.

---

## 트랜잭션 격리 수준 (Isolation Level)
격리 수준은 트랜잭션 간 간섭 정도를 조정하며, 격리 수준이 높을수록 간섭은 줄어들지만 성능은 저하된다.

| **격리 수준**       | **설명**                                                   | **문제 방지**              |
|--------------------|---------------------------------------------------------|---------------------------|
| **Read Uncommitted** | 커밋되지 않은 데이터도 읽을 수 있음.                       | 없음                     |
| **Read Committed**   | 커밋된 데이터만 읽음.                                     | Dirty Read 방지           |
| **Repeatable Read**  | 동일 데이터 조회 시 항상 같은 값 반환.                     | Non-repeatable Read 방지  |
| **Serializable**     | 가장 높은 수준, 트랜잭션을 순차적으로 실행.                 | Phantom Read 방지         |

---

## 자주 묻는 면접 질문
1. 트랜잭션의 4가지 특징(ACID)을 설명하세요.  
2. 트랜잭션의 격리 수준과 각각의 차이를 말해보세요.  
3. 데이터베이스에서 트랜잭션이 필요한 이유는 무엇인가요?  
4. 트랜잭션 중간에 장애가 발생하면 어떻게 처리되나요?  
5. Dirty Read, Non-repeatable Read, Phantom Read의 차이를 아나요?

---

## 한 줄 요약
트랜잭션은 데이터의 **일관성**과 **무결성**을 보장하는 작업 단위이며, **ACID** 특성과 적절한 **격리 수준** 설정이 필수적이다.
