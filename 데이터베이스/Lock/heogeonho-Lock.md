# 📌 데이터베이스의 **Lock(잠금)**

데이터베이스에서 **Lock(잠금)** 은 여러 사용자가 동일한 데이터를 동시에 접근하는 상황에서 **데이터의 무결성과 일관성을 유지하기 위해** 사용됨.  
또한, **Lock을 활용하여 트랜잭션의 격리성(Isolation)을 구현**할 수 있음.

---

> 내용 흐름은 제가 이해하기 쉬웠던 순서대로 정리해봤습니다.  
> 먼저 Lock을 **동시성 제어 방식에서의 적용**관점에서 소개하고, Locking 단위와 Lock의 유형에 대하여 정리해봤습니다.

## 🔹 **1. 동시성 제어 방식에서의 Lock**
Lock을 적용하는 방식은 **낙관적 락(Optimistic Lock)** 과 **비관적 락(Pessimistic Lock)** 으로 나뉨.

### **낙관적 락 (Optimistic Lock)**
- 데이터를 수정하기 전에 Lock을 걸지 않고, **데이터 충돌 가능성이 낮다고 가정**하는 방식.
- 데이터를 수정할 때(예: `UPDATE`) **버전 체크**나 **타임스탬프 비교**를 수행하여 충돌 여부를 확인함.

✔ **사용 사례**
- **읽기 위주의 시스템** (예: 게시판, 데이터 조회가 많은 웹 애플리케이션)
- **동시성이 높고, 충돌이 적은 경우 성능이 유리**

✔ **JPA에서 활용 예시 (`@Version`)**
```java
@Entity
public class Sample {
  @Id
  @GeneratedValue
  private Long id;

  private String name;

  @Version // 낙관적 락을 위한 버전 관리
  private int version;
}
  ```


### **비관적 락 (Pessimistic Lock)**
- 데이터가 **자주 수정될 가능성이 높다고 가정**하고, **수정 전에 Lock을 먼저 확보**하는 방식.
- 다른 트랜잭션이 접근하지 못하도록 **Row-Level Lock 또는 Table-Level Lock**을 설정함.

✔ **사용 사례**
- **경합이 많은 환경** (예: 계좌 이체, 재고 관리 시스템)
- **데이터 충돌이 빈번한 경우 안정성을 보장**

✔ **JPA에서 활용 예시 (`@Lock`)**
```text
@Lock(LockModeType.PESSIMISTIC_WRITE) 또는 PESSIMISTIC_READ를 명시
```

### 낙관적 락 vs. 비관적 락 비교표

| 제목         | 낙관적 락              | 비관적 락           |
|------------|--------------------|-----------------|
| Locking 시점 | 데이터 변경 시           | 조회 시            |
| 경합 처리 방식   | 변경 충돌 발생 시 예외 처리   | Lock 획득 후 변경    |
| 성능         | 읽기 위주 시스템에 유리      | 쓰기 경합이 많은 경우 유리 |
| 주요 사용 사례   | 조회가 많고, 변경이 적은 시스템 | 동시 수정이 빈번한 환경   |

## 🔹 **2. Locking 단위 (잠금을 어디에 둘 것인가)**
Lock은 **적용 범위에 따라** 네 가지 수준(Level)으로 구분됨.

### ✅ **(1) Database-Level Lock (데이터베이스 수준 잠금)**
- 데이터베이스 전체에 Lock을 설정하여, 하나의 트랜잭션이 DB를 사용하는 동안 **다른 트랜잭션이 접근하지 못하도록 함**.

✔ **특징**
- **Shared Lock(S Lock)**이 주로 사용됨.
- **데이터베이스 삭제(DROP) 방지**나 **백업 중 데이터 변경 방지** 등의 목적으로 사용됨.
- **멀티 사용자 환경에서는 속도가 느려서 잘 사용되지 않음**.

---

### ✅ **(2) Table-Level Lock (테이블 수준 잠금)**
- 특정 테이블(릴레이션)에 대한 전체적인 Lock을 설정하여, 해당 테이블을 **하나의 트랜잭션이 독점하도록 함**.

✔ **특징**
- **여러 사용자가 동일한 테이블을 사용해야 하는 멀티 사용자 DBMS에는 적합하지 않음**.
- **테이블 삭제(DROP) 또는 스키마 변경 방지를 위해 사용**될 수 있음.
- **5가지 Lock 유형이 적용됨** → `Exclusive (X)`, `Shared (S)`, `Intent Exclusive (IX)`, `Intent Shared (IS)`, `Shared Intent Exclusive (SIX)`

---

### ✅ **(3) Page-Level Lock (페이지 수준 잠금)**
- **고정 크기(Page) 단위**로 Lock을 설정하여, 특정 페이지의 데이터에 대한 접근을 제한.

✔ **특징**
- **멀티 사용자 환경에서 적절한 성능과 동시성을 제공**함.
- **Intent Shared Lock (IS)** 또는 **Intent Exclusive Lock (IX)**을 사용하여 페이지를 보호할 수 있음.
- **한 페이지(Page) 안에 여러 개의 행(Row)이 포함될 수 있음**.

---

### ✅ **(4) Row-Level Lock (행 수준 잠금)**
- **가장 세밀한 수준**의 Lock으로, 특정 **행(Row) 단위로 잠금**을 설정.

✔ **특징**
- **가장 세밀한 Lock이지만, 비용이 크고 성능 저하 가능성이 있음**.
- **동시성을 높일 수 있지만, Deadlock 위험이 증가**함.
- **Exclusive(X), Shared(S), Update(U) Lock을 적용할 수 있음**.


## 🔹 3. Lock의 유형
Lock은 목적과 사용 방식에 따라 여러 가지 유형으로 구분될 수 있음
  
| Lock 유형                             | 설명                                                      | 특징                             |
|-------------------------------------|---------------------------------------------------------|--------------------------------|
| Exclusive (X) Lock	                 | 해당 데이터에 대한 WRITE 작업을 허용하며, 다른 트랜잭션이 접근하지 못하도록 함	        | 한 번에 하나의 트랜잭션만 사용 가능           |
| Shared (S) Lock	                    | 해당 데이터에 대한 READ 작업만 허용, 여러 트랜잭션이 동시에 읽을 수 있음            | 	동시에 여러 사용자가 읽을 수 있지만, 수정은 불가능 |
| Intent Exclusive (IX) Lock	         | 특정 데이터의 하위 레벨에서 Exclusive Lock을 사용하겠다고 미리 선언            | 	테이블이나 페이지에 적용 가능              |
| Intent Shared (IS) Lock	            | 특정 데이터의 하위 레벨에서 Shared Lock을 사용하겠다고 미리 선언	              | 테이블이나 페이지에 적용 가능               |
| Shared Intent Exclusive (SIX) Lock	 | 하위 레벨에서 Shared Lock을 사용하면서, 일부 데이터에만 Exclusive Lock을 사용 | 	한 번에 하나의 트랜잭션만 가능             |
| Update (U) Lock	                    | 기존의 Shared Lock을 Exclusive Lock으로 변경하는 Lock             | 	Deadlock 방지 역할                |


## 🔹 **4. Deadlock 개념과 회피 방법**
### **Deadlock이란?**
- 두 개 이상의 트랜잭션이 서로가 필요로 하는 리소스를 **Lock으로 점유하고 있는 상태에서**,  
  **각각 다른 트랜잭션의 Lock이 풀리기를 기다리며 영원히 멈춰 있는 상태**를 의미함.
- 즉, 서로 **상대방이 가진 Lock을 필요로 하는 상황에서 발생**하는 **교착 상태(Deadlock)**.

- Deadlock은 **두 개 이상의 트랜잭션이 서로가 필요로 하는 리소스를 Lock으로 점유하고, 상대방의 Lock 해제를 기다리며 무한 대기하는 상황**에서 발생함.

✔ **Deadlock이 발생하는 주요 원인**
1. **Lock 순서 충돌**
  - 서로 다른 트랜잭션이 **서로가 필요로 하는 리소스를 다른 순서로 Lock**을 획득하면서 충돌이 발생.
  - 예:
    - 트랜잭션 A → Row 1 Lock → Row 2 Lock 대기
    - 트랜잭션 B → Row 2 Lock → Row 1 Lock 대기

2. **트랜잭션 실행 시간이 길어짐**
  - 트랜잭션이 **불필요하게 오래 실행**되면, 다른 트랜잭션이 필요한 리소스를 대기하는 시간이 길어지면서 Deadlock 위험 증가.

3. **Lock 범위가 커지는 경우 (Table-Level Lock)**
  - **Row-Level Lock보다 큰 단위(Table-Level Lock 등)를 사용하면 충돌이 증가**하여 Deadlock 가능성이 높아짐.

4. **교착 상태 발생 (Circular Wait Condition)**
  - 트랜잭션들이 서로가 가진 Lock을 기다리는 **순환 대기(Circular Wait)** 상태가 형성되면서 발생.
  - 예:
    - A는 자원 X를 점유하고 Y를 기다림.
    - B는 자원 Y를 점유하고 X를 기다림.

---

### **Deadlock 회피 방법**
Deadlock을 방지하기 위해 다음과 같은 기법을 사용할 수 있음.

#### 1️⃣ 트랜잭션 수행 순서 조정
#### 2️⃣ 짧은 트랜잭션 유지
#### 3️⃣ Lock Timeout 설정
#### 4️⃣ 낙관적 락 활용
#### 5️⃣ Deadlock이 발생했을 때 트랜잭션을 자동 재시도하도록 설계

---

3️⃣에 대한 코드 예시

MySQL

```sql
SET innodb_lock_wait_timeout = 10;
```

java
```java
try {
    entityManager.setProperty("javax.persistence.lock.timeout", 5000); // 5초 대기 후 실패
} catch (LockTimeoutException e) {
    System.out.println("Deadlock 방지를 위해 트랜잭션 종료");
}
```


## 📌 **참고 자료**
-  **[GeeksforGeeks - Levels of Locking in DBMS](https://www.geeksforgeeks.org/levels-of-locking-in-dbms/)**
-  **[Velog - 데이터베이스 락(Lock)의 종류와 역할](https://velog.io/@koo8624/Database-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EB%9D%BDLock%EC%9D%98-%EC%A2%85%EB%A5%98%EC%99%80-%EC%97%AD%ED%95%A0)**
-  **[SQL Server Intent Locks Explained](https://www.sqlpassion.at/archive/2016/05/16/why-do-we-need-intent-locks-in-sql-server/)**