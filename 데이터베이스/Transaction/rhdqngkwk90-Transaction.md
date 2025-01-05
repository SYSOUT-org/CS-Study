## 🤔 트랜잭션이란?

데이터베이스의 ***상태***를 변화시키기 위해서 수행하는 ***작업의 단위***를 뜻한다.
</br></br>

> 데이터베이스는 질의어(SQL)를 이용하여 상태가 변경된다. </br>
> 다만, 작업의 단위는 한번에 많은 질의어 명령문을 포함하게 된다. </br> 
> 명령어의 작업 순서에 따라 다른 전혀 다른 결과가 나오기 때문에 트랜잭션 설계를 잘 해야 한다.

###### 예) 조회 - 수정 - 조회 과정에서 중간에 삭제가 동시에 이루어지면 수정 후 조회의 값이 의도와 달라진다.

</br></br>


## ✨ 트랜잭션 상태
<img src="https://github.com/user-attachments/assets/0a877bc9-216a-4dc3-b6b9-a4c5075a8fe5" alt="트랜잭션 상태" width="700"/>


- **활성(Active)**
    - 트랜잭션이 정상적으로 실행 중인 상태

###### <작업 성공 시>

- **부분 완료(Partially Committed)**
    - 트랜잭션의 마지막까지 실행되었지만, Commit 연산이 실행되기 직전의 상태

- **완료(Committed)**
  - 트랜잭션이 성공적으로 종료되어 Commit 연산을 실행한 후의 상태

###### <작업 실패 시>

- **실패(Failed)**
  - 트랜잭션 실행에 오류가 발생하여 중단된 상태

- **철회(Aborted)**
  - 트랜잭션이 비정상적으로 종료되어 Rollback 연산을 수행한 상태


​

👉 위의 내용을 코드 실습으로 살펴본다.

​

테스트 테이블 TBL_USER을 사용한다.

<img src="https://github.com/user-attachments/assets/e603a262-1efa-4e4b-80dc-d35968da5c6a" alt="TBL_USER 초기 상태" width="400"/>

```sql
-- 활성(Active)
START TRANSACTION; -- 트랜잭션 시작

select * from TBL_USER; -- 초기 상태 확인
insert into TBL_USER values('10104', '홍길동', 'D', 'Y', 30, 'M'); -- 데이터1 추가
select * from TBL_USER; -- 데이터1 추가 상태 확인
```
<img src="https://github.com/user-attachments/assets/f83393cf-bf4d-49a5-9b79-12967e36e6db" alt="데이터1 추가 상태" width="400"/>

```sql
SAVEPOINT save_point; -- save_point 지정

insert into TBL_USER values('10105', '김갑수', 'A', 'N', 42, 'M'); -- 데이터2 추가
select * from TBL_USER; -- 데이터2 추가 상태 확인
```
<img src="https://github.com/user-attachments/assets/a55c3cef-34a8-4879-8326-2dbc556247d5" alt="데이터2 추가 상태" width="400"/>

```sql
-- 철회(Aborted)
ROLLBACK TO save_point; -- save_point 실행 전 상태로 롤백함
select * from TBL_USER; -- 데이터2 없는 상태 확인
```
<img src="https://github.com/user-attachments/assets/faa5e2d8-b5a4-439e-b5d9-b18bff1231bf" alt="save_point 실행 전 상태로 롤백" width="400"/>

```sql
-- 철회(Aborted)
ROLLBACK; -- 트랜잭션을 취소하고 START TRANSACTION 실행 전 상태로 롤백
select * from TBL_USER; -- 초기 상태 확인
```
<img src="https://github.com/user-attachments/assets/e603a262-1efa-4e4b-80dc-d35968da5c6a" alt="TBL_USER 초기 상태" width="400"/>

```sql
-- 부분완료(Partially Committed)
insert into TBL_USER values('10106', '박민수', 'B', 'N', 22, 'M'); -- 데이터3 추가
select * from TBL_USER; -- 데이터3 추가 상태 확인

COMMIT; -- 트랜잭션을 DB에 적용

-- 완료(Committed)
```
<img src="https://github.com/user-attachments/assets/eb511dfa-7f44-4fec-9499-196677b1c241" alt="데이터3 추가 상태" width="400"/>

</br></br>


## 📌 트랜잭션의 특징 (ACID)

- **원자성 (***A***tomicity)**
  - 연산은 모두 반영되거나, 모두 반영되지 않아야 한다. 
  - All or Nothing


- **일관성 (***C***onsistency)**
  - 작업 처리 결과는 항상 일관적인 상태를 유지해야 한다.


- **독립성 (***I***solation)**
  - 다른 트랜잭션의 작업에 끼어들거나 서로 영향을 줄 수 없다. 👉 트랜잭션 격리수준으로 관리


- **지속성 (***D***urability)**
  - 트랜잭션이 완료된다면, 결과는 영구적이어야 한다.

</br></br>


## 🙏 트랜잭션 격리 수준 (Isolation level)

트랜잭션 격리 수준에서 중요한 키워드는 ***동시성***과 ***데이터 정합성***이다.

​

###### ↑ 격리수준  낮다

- **READ UNCOMMITTED (커밋되지 않은 읽기)**
  - COMMIT, ROLLBACK 여부에 상관없이 트랜잭션에서 값을 읽을 수 있다. 
  - 💥 DIRTY READ 발생

- **READ COMMITTED (커밋된 읽기)**
  - COMMIT이 된 데이터만 읽는다. ✔RDB 기본 
  - 💥 NON-REPEATABLE READ 발생

- **REPEATABLE READ (반복 가능한 읽기)**
  - 트랜잭션이 생성되기 이전의 트랜잭션에서 COMMIT 된 데이터만 읽는다. ✔MySQL/MariaDB 기본 
  - 💥 PHANTOM READ 발생

- **SERIALIZABLE (직렬화 가능)**
  - 데이터를 접근할 때, 항상 Lock을 걸고 데이터를 조회한다. = 성능 문제로 잘 안 씀

###### ↓ 격리수준 높다

​


👉트랜잭션 격리 수준을 그림과 함께 자세히 살펴본다.

​


### 1️⃣ READ UNCOMMITTED (커밋되지 않은 읽기)

<img src="https://github.com/user-attachments/assets/2cb4bf43-342b-4bb4-9fcf-63495c6b5bad" alt="READ UNCOMMITTED" width="800"/>

- 5번 트랜잭션이 커밋되지 않은 시점에서, 2번 트랜잭션이 변경된 데이터에 접근할 수 있다.
- 가장 낮은 수준의 격리 수준으로 일반적으로 사용하지 않는다.
- 🤔 위의 격리 수준을 사용했을 때 어떤 문제가 발생할 수 있을까?
</br>

<img src="https://github.com/user-attachments/assets/02fcfa56-a964-42cb-bc3f-91e0aa9bb5aa" alt="READ UNCOMMITTED - DIRTY READ" width="800"/>

💥 DIRTY READ 발생
- 2번 트랜잭션이 데이터를 조회한 후, 5번 트랜잭션이 오류가 발생하여 ROLLBACK을 하게 된다면 데이터 부정합을 발생시킬 수 있다.
- 이와 같이 완료되지 않은 작업에 다른 트랜잭션이 접근하여 동시에 데이터를 읽는 문제를 더티읽기(Dirty Read)라고 한다.
</br>


​

### 2️⃣ READ COMMITTED (커밋된 읽기)

<img src="https://github.com/user-attachments/assets/932af5f3-4a99-4ca6-a03f-0d6d42db60d2" alt="READ COMMITTED" width="800"/>

- 2번 트랜잭션은 커밋된 5번 트랜잭션 데이터만 접근 가능하다.
- MySQL을 제외하고 대부분 이를 기본 격리 수준으로 사용한다.
- 5번 트랜잭션이 데이터를 '김두리'로 update 한 후 Commit 하지 않았을 때, 2번 트랜잭션은 update 전인 '김하나'라는 값을 조회한다. 이는 Undo 개념 때문이다.
</br>

<hr style="width: 50%;"/>

**💡 Undo**

트랜잭션은 로그가 반드시 남고, 복구는 로그를 기반으로 처리된다. 로그는 크게 두 가지가 있다.

<img src="https://github.com/user-attachments/assets/b77dac73-8a6b-4720-9c27-011f2b0e294e" alt="REDO" width="600"/>
<img src="https://github.com/user-attachments/assets/c0590d7e-9ecd-4649-b358-d87e2681147f" alt="UNDO" width="600"/>

- Redo Log 
  - 다시 실행의 뜻, 오류에 의한 복구에 사용 
  - 커밋된 트랜잭션에 대한 정보를 가지고 있다.

- Undo Log 
  - 실행 취소의 뜻, 롤백을 위해 사용 
  - 변경전 값과 이에 대한 PK를 가지고 있다.
</br>
​

😮UNDO 영역이라고 말한 이유?

Undo Log는 Undo Log Buffer 형태로 메모리에 저장, 특점 시점에 디스크에 저장된 Undo Log File에 I/O 작업으로 쓰여지기 때문이다. 데이터에 변경사항이 생길때마다 Disk에 I/O 작업을 하는 것보다 메모리에 입력하고 읽는 것이 속도와 리소스 측면에서 유리하기 때문!

🤨그런데 Undo Log가 과도하게 쌓이면 잠금 없는 일관된 읽기를 위해 탐색 비용이 발생해 읽기 성능이 떨어지게 되고, 다른 트랜잭션들이 동일한 자원을 필요로 하는 경우 데드락이 발생할 가능성이 높아진다. 따라서 트랜잭션 단위를 짧게 가져가야 하는 이유를 Undo 개념과 함께 이해할 수 있다.

<hr style="width: 50%;"/>

🤔 그렇다면 이번 격리 수준은 문제가 없을까?

<img src="https://github.com/user-attachments/assets/6c97db60-478d-4503-a710-d1fcb9dea2a6" alt="READ COMMITTED - NON-REPEATABLE READ" width="800"/>

- 💥 NON-REPEATABLE READ 발생 
  - 2번 트랜잭션에서 동일한 select 쿼리를 실행했을 때, 다른 결과가 조회되는 것을 알 수 있다. 5번 트랜잭션의 커밋 전, 후에 select 쿼리를 실행했기 때문이다. 
  - 이는 트랜잭션의 일관성을 해치는 문제로, 두 가지 해결 방법이 존재한다. 
    - 5번 트랜잭션이 commit이나 rollback이 될 때까지 2번 트랜잭션의 실행을 지연하는 것 (Oracle)
    - Multiversion Concurrency Control(MVCC)을 사용하는 REPEATABLE READ를 사용하는 것 (MySQL)
</br>
​

### 3️⃣ REPEATABLE READ (반복 가능한 읽기)

<img src="https://github.com/user-attachments/assets/e6f6b880-c404-440a-b1ff-27082b287a12" alt="REPEATABLE READ" width="800"/>

- 자신보다 낮은 트랜잭션 번호에서 커밋한 데이터만 읽을 수 있는 격리 수준이다.
- 2번 트랜잭션은 2보다 작은 번호의 트랜잭션에서 커밋한 데이터만 읽을 수 있기 때문에, 5번 트랜잭션에서 변경한 내용은 조회할 수 없다. 따라서 2번 트랜잭션에서 동일한 select 쿼리를 실행했을 때, 같은 결과가 조회되는 것을 확인할 수 있다.
- 이는 트랜잭션 ID를 통해 Undo 영역의 데이터를 스냅샷처럼 관리하여 동일한 데이터를 보장하는 MVCC 때문이다.
</br>

<hr style="width: 50%;"/>

**💡 Multi-Version Concurrency Control (MVCC) - 다중 버전 동시성 제어**
- 동시성 문제가 발생할 수 있는 상황에서 잠금을 사용하지 않고 읽기 일관성을 제공하는 목표를 가진 기능
- 하나의 데이터에 대해 여러 버전의 데이터를 동시에 가지고 있는 것을 말한다.
- MVCC를 통해 특정 시점에서의 테이블 데이터를 불러올 수 있다. 트랜잭션에서 특정 레코드를 조회하면 해당 레코드 트랜잭션 번호를 참고해 자신보다 먼저 실행된 트랜잭션의 데이터만을 조회한다.

<hr style="width: 50%;"/>

💡 Exclusive Lock - 배타적 잠금/쓰기 잠금 
- Oracle의 Non Repeatable Read 문제 해결 방법
- 특정 레코드나 테이블에 대해 다른 트랜잭션에서 읽기, 쓰기 작업을 허용하지 않는 Lock

<hr style="width: 50%;"/>

🤔 그렇다면 이번엔 어떤 문제가 발생할 수 있을까?

미완~


--- 
🔗 참고링크
<small>
https://blog.naver.com/ohhappyday030/223710939517
</small>
