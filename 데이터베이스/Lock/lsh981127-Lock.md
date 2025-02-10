# 트랜잭션 락(Lock)이란?

### 락(Lock)이란?

- 여러 커넥션에서 동시에 동일한 자원을 요청하는 경우, 순서대로 하나의 커넥션만 접근할 수 있게 해주는 기능
- 동시에 데이터베이스에 접근하는 상황(**동시성**)에서 데이터 무결성과 일관성을 지키기 위한 용도

### 락의 종류

- **공유 Lock(Shared Lock, Read Lock, S-Lock)**
- **배타 Lock(Exclusive Lock, Write Lock, X-Lock)**

### 공유 락(Shared Lock, Read Lock, S-Lock)

> 읽기 작업(Read)을 위해 잠그는 락
>
- 여러 사용자가 동시에 데이터를 읽어도 일관성에 영향을 주지 않기 때문에, **공유 Lock**간에는 동시에 접근이 가능하다
- **단, 다른 배타 Lock은 막는다.**

### 배타 락(Exclusive Lock, Write Lock, X-Lock)

> 데이터 변경하는 작업(Write)을 위해 잠그는 락
>
- 쓰기 작업을 진행하기 위해 **다른 세션의 모든 접근(SELECT, INSERT, …)** 을 막는다.
    - 다른 세션이 잘못된 값을 읽을 수도 있기 때문에 다른 세션의 **공유 Lock** 획득을 막는다.
    - 다른 세션이 기존 작업 중인 값이 달라질 수 있기 때문에, 다른 세션의 **배타 Lock** 획득을 막는다.

### **블로킹 상태(Blocking)**

> **Lock 간의 경합이 발생해 특정 트랜잭션이 작업을 진행하지 못하고 대기하는 상태**
>
- DB 작업 시, 데이터 무결성, 정합성을 지키기 위해 트랜잭션을 사용
    - Lock 또한 트랜잭션 안에서 잠기고, 해제됨
- 발생하는 경우(트랜잭션 A - 트랜잭션 B인 경우 B가 Blocking 상태에 들어간다)
    - 공유 Lock - **배타 Lock**
    - 배타 Lock - **공유 Lock**
    - 배타 Lock - **배타 Lock**

![image.png](attachment:72641f25-27ea-4c39-89c5-9efa612bb8f6:image.png)

- 위 예시에서 Blocking 상태의 트랜잭션 B는 **어떤 작업들도 실행하지 못하고 대기하는 상태**가 된다.

### 블로킹 상태가 해결되는 방법

- Blocking 돌입 시, 기존 Lock을 설정한 **트랜잭션이 Commit or Rollback** 되는 방법밖에 없다.
    - Blocking 상태의 트랜잭션이 Lock의 Timeout 시간이 지나면 에러가 발생하고 트랜잭션 롤백시킨 뒤, 종료된다.

### 데드 락(Dead Lock, 교착 상태)

> 두 트랜잭션 **모두 Blocking 상태에 진입하여 서로의 블로킹을 해결할 수 없는 상태**
>
- 예시
    - 트랜잭션 A는 트랜잭션 B에 대한 블로킹 상태
        - A는 트랜잭션 B가 Commit/Rollback 될때까지 대기
    - 트랜잭션 B는 트랜잭션 A에 대해 블로킹 상태
        - B는 트랜잭션 A가 Commit/Rollback 될때까지 대기
    - 즉, 서로가 서로의 종료를 기다리기에 서로의 블로킹 상태를 영원히 해결할 수 없는 상태가 된다.

![image.png](attachment:8ab5ee27-2386-4dc7-a588-b8598f5a9735:image.png)

위 그림은 데드락 상황에 대한 예시

과정 설명은 이와 같다.

1. 세션 A : Coupon 테이블 배타 Lock 설정 / 세션 B : Member 테이블에 배타 Lock 설정
2. 세션 A : Member 테이블에 공유 Lock 설정 ⇒ B가 이미 배타 Lock 건 상태
    1. A는 Member 테이블에 대해 Blocking 상태 진입
    2. Member 테이블에 건 B의 배타 Lock이 끝나야 Blocking 상태 해제
3. 세션 B : Coupon 테이블에 공유 Lock 설정 ⇒ A가 이미 배타 Lock 건 상태
    1. B는 Coupon 테이블에 대해 Blocking 상태 진입
    2. Coupon 테이블에 건 A의 배타 Lock이 끝나야 Blocking 상태 해제
4. 트랜잭션 A - B간 서로의 종료를 기다리고 있기에 무한정 대기하는 **데드락 상태 진입**

### 데이터베이스에서 동시성 제어 방식(Concurrency Control Mechanism)

- 어떻게 충돌을 방지할 것인가? 를 결정하는 방식이다.

### 종류

- **낙관적 락(Optimistic Lock)**
- **비관적 락(Pessimistic Lock)**

### **낙관적 락(Optimistic Lock)**

> 자원에 Lock을 걸지 않은 상태로, **동시성 문제가 발생할 시 그때 그때 문제를 해결**하는 방식
>
- 수정할 때, **수정했다는 것을 명시**함으로써 다른 트랜잭션이 동일한 조건으로 값을 수정할 수 없게 만드는 것
- **version과 같은 별도 컬럼을 추가**해 충돌을 막는다.
    - 버전 관리 및 언제 버전이 업데이트 됬는지 timestamp를 붙여서 충돌을 확인/방지

- DB에서 충돌을 해결하는 게 아니라, **애플리케이션 단에서 처리**한다.
    - UPDATE 실패해도 예외를 던지지 않고, 그냥 0 개의 row를 업데이트한다
    - 이때, 여러 작업이 묶은 트랜잭션들이 실패하는 경우 개발자가 롤백해야함

JPA에서 낙관적 락을 사용하는 방법

> `@version` 어노테이션으로 낙관적 락을 사용할 수 있다.
문제가 발생한 경우, `ObjectOptimisticLockingFailureException` 예외가 발생하며 이를 처리해줘야한다.
>

### 비관적 락(Pessimistic Lock)

> 트랜잭션이 시작할 때, **공유 락(S-Lock) 또는 배타 락(X-Lock)을 걸어 동시성을 제어**하는 방식
>
- **Repeatable Read / Serializable** 정도의 격리성 수준을 제공한다.
- 공유 락, 배타 락을 활용해 트랜잭션에 Blocking 상태를 만들어 동시성을 제어하는 방식
- *Blocking 상태에서 사용한 그림 예시가 비관적 락의 예시*

![image.png](attachment:8ab5ee27-2386-4dc7-a588-b8598f5a9735:image.png)

### 생각

> **데이터 무결성 및 정합성의 중요도에 따라 낙관적/비관적 락을 선택하자**
>

### 참고 자료

https://ksh-coding.tistory.com/121

https://velog.io/@bagt/Database-%EB%82%99%EA%B4%80%EC%A0%81-%EB%9D%BD-%EB%B9%84%EA%B4%80%EC%A0%81-%EB%9D%BD