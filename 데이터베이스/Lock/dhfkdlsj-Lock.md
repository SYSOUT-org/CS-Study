Lock : 잠그다

왜 데이터베이스 Lock을 하고, 또 어떻게 하는가?

## **📌 왜 Lock을 사용할까?**

![image](https://github.com/user-attachments/assets/be93fe45-4fec-4b4e-bc0c-49a3fd82d3b2)


-   재고는 **1개밖에 없었지만**, 두 사람이 동시에 주문하여 **둘 다 성공하는 문제 발생**
-   이러한 **데이터 무결성 문제를 방지하기 위해** DBMS에서는 **동시성 제어**를 수행
-   동시성 제어: **트랜잭션들이 동시에 수행될 때, 데이터 일관성을 유지하도록 접근을 제어하는 것**

어떻게?  
Lock을 이용

---

## **🔑 Lock의 종류**

### **1️⃣ 낙관적 잠금(Optimistic Lock)**

> 데이터 갱신 시 **경합이 발생하지 않을 것**이라고 가정  
> 한 사용자가 업데이트를 완료하면, 동시 업데이트 확약을 시도하는 **다른 사용자에게 충돌이 있음을 알림**

![image](https://github.com/user-attachments/assets/0619bebe-dbe7-4ee3-ad45-881733fdb73c)


**✅ 동작 방식**

1.  첫 **Read 시 version을 읽음**
2.  구매 완료 후 **다시 version을 읽음**
3.  **처음 읽었던 version과 같다면 업데이트 진행**
4.  **다르면 충돌 발생 → Rollback 수행**

**📌 특징**

-   **동시 업데이트가 거의 없는 경우**에 사용하면 좋음
-   충돌 발생 시 롤백이 필요함

---

### **2️⃣ 비관적 잠금(Pessimistic Lock)**

> 동일한 데이터를 **동시에 수정할 가능성이 높다고 가정**  
> 다른 사용자는 **잠금이 해제될 때까지 대기해야 함**

![image](https://github.com/user-attachments/assets/bd28fa08-3c5a-443f-8190-0051a2660fec)


**✅ 동작 방식**

1.  **데이터 접근 전 Lock 설정**
2.  **모든 작업 완료 후 Unlock 전까지 다른 트랜잭션 대기**

**📌 특징**

-   **동시 업데이트가 빈번한 경우** 유리
-   **Rollback이 어려운 외부 시스템과 연동**된 경우 적합

---

## **🔐 비관적 잠금 연산의 종류**

### **🔹 공유 Lock (Shared Lock, S Lock)**

-   **Read 가능 / Write 불가능**
-   여러 트랜잭션이 **동시에 접근 가능**

![image](https://github.com/user-attachments/assets/e61df8b1-5a64-49f2-865a-987ea583040d)


### **🔹 배타 Lock (Exclusive Lock, X Lock)**

-   **Read & Write 모두 가능**
-   락을 설정한 **트랜잭션만 독점적으로 접근 가능**

![image](https://github.com/user-attachments/assets/849e8b78-2f72-48a2-ae89-df8c92fc5c4b)


**📌 공용 Lock은 양립 가능하지만, 배타 Lock은 다른 락과 양립할 수 없음**

---

## **📏 Lock의 단위**

![image](https://github.com/user-attachments/assets/7f377198-21de-484c-9e79-a9b7d3de82a5)


> **Lock의 단위를 크게 가져가면** 구현은 쉬워지지만 **병행성이 떨어짐**  
> **Lock의 단위를 작게 가져가면** 병행성은 높아지지만 **구현이 복잡해짐**

🔹 **Lock 단위는 DBMS마다 다름**

---

## **🚨 블로킹(Blocking)**

-   **Lock 간 경합 발생 → 특정 트랜잭션이 대기하는 상태**
-   **배타 Lock**(X Lock)은 다른 Lock과 함께 사용할 수 없기 때문에 발생
-   **Commit 또는 Rollback이 실행될 때 블로킹 해제**

![image](https://github.com/user-attachments/assets/7963da7d-5c4b-420d-b181-88e3c50ed9d4)


### **✅ 해결 방안**

1.  **트랜잭션을 짧게 정의**
2.  **동일 데이터를 갱신하는 트랜잭션을 동시에 수행하지 않도록 설계**
3.  **Lock Timeout을 설정하여 일정 시간 후 자동 해제**

---

## **⚠ 교착 상태(Deadlock)**

> **두 트랜잭션이 서로의 락을 기다리면서 영원히 멈추는 상태**

![image](https://github.com/user-attachments/assets/768f456f-5d97-4e62-acbd-16bb6ca51104)


### **✅ 해결 방안**

1.  **트랜잭션 진행 방향을 동일하게 처리**
    -   예) 트랜잭션 A와 B가 **자원 1과 2에 접근**
    -   무조건 **1 → 2 순서로 락을 걸도록 강제**
2.  **트랜잭션 처리 속도를 최소화**
3.  **Lock Timeout을 설정하여 일정 시간 후 해제**

---

## **📚 참고 문헌**

-   [🔗 유튜브 영상](https://www.youtube.com/watch?v=ZXV6ZqMyJLg)
-   [🔗 블로그](https://ksh-coding.tistory.com/121)
