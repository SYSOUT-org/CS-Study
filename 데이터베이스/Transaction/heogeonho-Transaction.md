# 트랜잭션 (Transaction)

트랜잭션은 데이터베이스 시스템에서 **논리적 작업 단위**로, 데이터의 일관성과 무결성을 보장. 트랜잭션은 **모두 성공하거나 모두 실패**해야 하며, 데이터베이스의 상태를 **일관성 있는 상태**로 유지.

---

## 트랜잭션의 특징: ACID

1. Atomicity (원자성)
    - 작업이 **전부 성공**하거나 **전부 실패**해야 함.
    - 실패 시, 모든 작업은 원래 상태로 되돌아감.
    - 예: 은행 계좌 간 송금에서 일부만 성공하면 안 됨.


2. Consistency (일관성)
    - 트랜잭션 전후로 데이터베이스는 항상 **일관성** 있는 상태를 유지해야 함.
    - 예: 은행 계좌 총합은 송금 전후 동일해야 함.


3. Isolation (고립성)
    - 여러 트랜잭션이 동시에 실행되어도, 서로의 영향을 받지 않아야 함.
    - 예: 두 사용자가 동시에 동일 데이터를 수정해도 충돌이 없어야 함.


4. Durability (지속성)
    - 트랜잭션 완료 후 데이터는 시스템 장애 발생 시에도 **영구히 저장**됨.
    - 예: 전원이 꺼져도 데이터는 보존.

---

## 트랜잭션의 상태

![image](https://github.com/user-attachments/assets/547ade73-045c-4644-924c-9801696835d3)

1. **Active**: 트랜잭션이 실행 중.
2. **Partially Committed**: 작업이 완료되었으나 영구적이지 않은 상태.
3. **Committed**: 트랜잭션 성공 후 데이터가 영구적으로 저장됨.
4. **Failed**: 오류로 인해 트랜잭션이 실패한 상태.
5. **Aborted**: 실패한 트랜잭션이 롤백되어 이전 상태로 복구된 상태.

---

## 트랜잭션 제어 언어 (TCL)

1. **BEGIN TRANSACTION**: 트랜잭션 시작.
2. **COMMIT**: 트랜잭션 성공 시 데이터 변경 내용 반영.
3. **ROLLBACK**: 트랜잭션 실패 시 데이터 변경 취소.
4. **SAVEPOINT**: 트랜잭션 중간 상태 저장, 특정 지점으로 복구 가능.

---

## 트랜잭션 격리 수준 (Isolation Levels)

### 1. Read Uncommitted
- **커밋되지 않은 데이터**를 다른 트랜잭션에서 읽을 수 있음.
- 문제점: Dirty Read (잘못된 데이터 읽기).

### 2. Read Committed
- 커밋된 데이터만 읽을 수 있음.
- 문제점: Non-repeatable Read (같은 데이터를 여러 번 읽을 때 값이 다름).

### 3. Repeatable Read
- 같은 데이터를 여러 번 읽어도 항상 동일한 값을 반환.
- 문제점: Phantom Read (조건에 맞는 데이터 추가/삭제로 결과가 달라짐).

### 4. Serializable
- 가장 높은 격리 수준. 모든 트랜잭션이 순차적으로 실행되는 것처럼 보임.
- 동시성 저하 가능.

---

## 데이터 무결성 문제 사례와 해결

### Dirty Write (더티 라이트)
![image](https://github.com/user-attachments/assets/d552a741-c977-4d3c-949c-68bbc581e9da)


- **사례**:
   - 트랜잭션 A와 트랜잭션 B가 같은 데이터를 동시에 수정.
   - 트랜잭션 A가 롤백되면, 트랜잭션 B는 잘못된 데이터를 사용하게 됨.

- **해결**:
   - **행 단위 잠금**을 통해 동일 데이터의 쓰기를 방지.
   - 다른 트랜잭션이 완료될 때까지 대기.

---

### Read Skew (읽기 왜곡)
![image](https://github.com/user-attachments/assets/8ad6a25b-cceb-46cd-8d7c-08c62e020f7d)


- **사례**:
   - 트랜잭션 A가 읽은 데이터가 트랜잭션 B에 의해 수정되거나 삭제.
   - 트랜잭션 A의 연산 결과가 왜곡될 가능성.

- **해결**:
   - **Repeatable Read** 이상 격리 수준 사용.

---

### **Phantom Read (팬텀 읽기)**

- **사례**:
   - 트랜잭션 A가 특정 조건의 데이터를 읽고 있는 동안, 트랜잭션 B가 데이터를 삽입 또는 삭제.
   - 트랜잭션 A의 두 번째 조회 결과가 처음과 다르게 나타남.

- **해결**:
   - **Serializable** 격리 수준 사용.
   - 레코드뿐 아니라 **범위 잠금(Range Lock)** 적용.

---

### **Lost Update (변경 유실)**
![image](https://github.com/user-attachments/assets/b0f2d831-ddf9-49c1-ade8-d60c66475ab1)

- **사례**:
   - 두 트랜잭션이 동시에 같은 데이터를 수정.
   - 하나의 트랜잭션 변경 내용이 다른 트랜잭션에 의해 덮어씌워짐.

- **해결**:
   - **원자적 연산** 사용 (DBMS가 동시 수정 요청을 순차적으로 처리).
   - **명시적 잠금**으로 수정 중인 데이터에 다른 트랜잭션 접근 차단.

---

## 트랜잭션과 로그

### Redo Log
- 변경 작업을 **디스크에 기록**하여 **지속성(Durability)** 보장.
- 시스템 장애 시 데이터를 복구.

### Undo Log
- 변경 전 데이터를 저장하여 **원자성(Atomicity)**과 **일관성(Consistency)** 보장.
- 롤백 시 데이터를 이전 상태로 복구.

---

## InnoDB에서의 트랜잭션 처리 흐름

InnoDB?
![image](https://github.com/user-attachments/assets/47c6e23e-fbce-431e-ba80-b9d75c989010)



1. **트랜잭션 시작**: Undo Log 준비.
2. **데이터 변경**:
    - 메모리(Buffer Pool)에 반영.
    - Undo Log에 이전 값 저장.
    - Redo Log Buffer에 변경 내용 기록.
3. **커밋**:
    - Redo Log를 디스크에 기록(fsync).
    - 데이터 변경 내용을 영구히 반영.
4. **롤백**:
    - Undo Log를 사용해 이전 상태로 복구.

---

## 트랜잭션의 장점

- **동시성 제어**: 여러 사용자의 요청을 효율적으로 처리.
- **데이터 무결성 보장**: 시스템 장애와 오류에 강함.
- **코드 단순화**: 복잡한 상태 관리 대신 트랜잭션을 활용.

---

# 스프링에서 트랜잭션 관리 (@Transactional)

## @Transactional 사용 방법

### 기본 사용법
```java
@Service
public class OrderService {

    @Transactional
    public void processOrder(Order order) {
        orderRepository.save(order); // 주문 데이터 저장
        paymentService.processPayment(order); // 결제 처리
    }
}
```



# 스프링에서 트랜잭션 관리 (@Transactional)

스프링은 **@Transactional** 어노테이션을 통해 트랜잭션 관리를 간편하게 처리할 수 있는 강력한 도구를 제공.    
이 어노테이션은 선언적 방식으로 트랜잭션 경계를 지정하며, 코드 간소화와 일관된 트랜잭션 처리를 가능하게 함.

---

## @Transactional 활용 및 옵션

### 주요 속성

---

#### 1. propagation (전파 레벨)
- 트랜잭션 전파 방식을 설정합니다.
- 예: 이미 실행 중인 트랜잭션에 참여할지, 새로운 트랜잭션을 시작할지 결정.

| Propagation Type | 설명                                            |
|------------------|-----------------------------------------------|
| REQUIRED         | 현재 트랜잭션이 존재하면 참여, 없으면 새로 생성 (기본값).            |
| REQUIRES_NEW     | 항상 새로운 트랜잭션을 생성. 기존 트랜잭션은 일시 정지.              |
| NESTED           | 중첩된 트랜잭션 생성. 내부 트랜잭션 롤백 시 외부 트랜잭션은 영향을 받지 않음. |
| SUPPORTS         | 트랜잭션이 존재하면 참여, 없으면 트랜잭션 없이 실행.                |
| MANDATORY        | 반드시 기존 트랜잭션에 참여. 없으면 예외 발생.                   |
| NEVER            | 트랜잭션 없이 실행. 기존 트랜잭션이 존재하면 예외 발생.              |
| NOT_SUPPORTED    | 트랜잭션 없이 실행. 기존 트랜잭션이 있으면 일시 정지.               |
```java
@Transactional(propagation = Propagation.REQUIRED)       // Default
@Transactional(propagation = Propagation.REQUIRES_NEW) 
@Transactional(propagation = Propagation.NESTED) 
@Transactional(propagation = Propagation.SUPPORTS) 
@Transactional(propagation = Propagation.MANDATORY) 
@Transactional(propagation = Propagation.NOT_SUPPORTED)
@Transactional(propagation = Propagation.NEVER)
```

---

#### 2. isolation (격리 수준)
- 트랜잭션 간의 격리 수준을 설정합니다.
- 데이터의 무결성을 보장하지만, 높은 수준에서는 성능 저하 가능.

| **Isolation Level** | **설명**                                |
|---------------------|---------------------------------------|
| READ_UNCOMMITTED    | 커밋되지 않은 데이터도 읽을 수 있음 (Dirty Read 허용). |
| READ_COMMITTED      | 커밋된 데이터만 읽을 수 있음 (기본값).               |
| REPEATABLE_READ     | 동일 데이터를 여러 번 읽어도 항상 같은 결과 반환.         |
| SERIALIZABLE        | 가장 높은 수준의 격리, 모든 트랜잭션이 순차적으로 실행.      |

```java
@Transactional(isolation = Isolation.READ_UNCOMMITTED)
@Transactional(isolation = Isolation.READ_COMMITTED)
@Transactional(isolation = Isolation.REPEATABLE_READ)
@Transactional(isolation = Isolation.SERIALIZABLE)
```
---

#### 3.	timeout:
- 트랜잭션이 지정된 시간 내에 완료되지 않으면 롤백.
```java
@Transactional(timeout = 5) // 5초 내에 완료되지 않으면 롤백
```
---

#### 4. readOnly:
- 읽기 전용 트랜잭션을 설정, 뱐경 작업이 허용 x, 성능 최적화에 도움
```java
@Transactional(readOnly = true)
```

# 개인적으로 추가 고민거리
결제 로직을 구현하면서 해당 부분에 대한 처리 방식에 대해 고민이 많았음

![image](https://github.com/user-attachments/assets/3941a768-8aa2-48a2-9cc3-75cae21fca33)
위 상황에서의 롤백 처리 시나리오에 대해 고민의 필요성을 느껴 추가 과제로 해보려고 함
추가로 Lock관련 내용과 @Transactional의 내부 동작을 알아보다가 시간 관계상 내용을 담지 못했는데 추후 추가 포스팅 해보겠습니다.


### 참고 자료
https://rebro.kr/162 
https://neocan.tistory.com/396   
https://www.youtube.com/watch?v=poyjLx-LOEU  
https://www.youtube.com/watch?v=ImvYNlF_saE  
https://www.youtube.com/watch?v=aX9c7z9l_u8  
<아래 자료는 다시 꼼꼼하게 읽어보기>   
https://medium.com/gdgsongdo/transactional-바르게-알고-사용하기-7b0105eb5ed6
