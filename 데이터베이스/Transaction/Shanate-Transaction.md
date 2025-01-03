---

## Transaction

: **DB의 상태**를 **변화**시키기 위해 수행하는 **작업**의 단위.

: 하나 이상의 작업을 묶어 처리하며, 이 작업은 모두 성공하거나 모두 실패해야 한다. → DB의 **정합성**을 지키기 위함이다.

---

다음과 같은 속성을 만족해야 한다.

**Atomicity**(원자성) : 트랜잭션 내의 연산은 데이터베이스에 **전부** 반영이 되거나 **전부** 반영이 되어서는 안 된다. 만일, 전체 반영이 되지 않았다면 해당 트랜잭션은 취소되어야 한다.
**Consistency**(일관성) : 트랜잭션 완료 후에 DB는 일관된 상태를 유지해야 한다.

**Isolaiton**(고립성) : 각 트랜잭션은 다른 트랜잭션으로부터 격리되어 있어야 하며, 동시에 실행되어도 서로 영향을 미쳐서는 안 된다.

**Durability**(내구성) : 트랜잭션이 성공하면 그 결과는 영구적으로 저장되어야 한다.

---

Commit 연산, Rollback 연산

Commit : 트랜잭션에서의 Commit은 트랜잭션이 끝난 후, DB가 일관성있는 상태에 있을 때 하나의 트랜잭션이 끝났음을 알려주는 연산이다. MySQL에서는 INSERT, DELETE, UPDATE 등의 명령문을 사용해서 데이터 상태를 바꿀 때마다 내부에서 자동적으로 Commit을 실행해 DB에 반영한다.

Rollback : 트랜잭션 처리가 비정상적으로 끝나 트랜잭션의 원자성이 어긋난 경우, 트랜잭션을 다시 시작하거나, 부분적으로 처리된 과정을 취소시킨다. 즉, DB의 일관성을 지켜주기 위해 후속조치를 하는 연산으로 보면 된다.

아래에서 설명을 보충한다.

---

## 트랜잭션의 상태

![[https://inpa.tistory.com/entry/MYSQL-📚-트랜잭션Transaction-이란-💯-정리](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98Transaction-%EC%9D%B4%EB%9E%80-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC)](https://prod-files-secure.s3.us-west-2.amazonaws.com/03205dcf-f90c-4bd9-8281-674e7b939609/4d12251c-118a-4bd7-abb7-5f9f24bd438f/image.png)

[https://inpa.tistory.com/entry/MYSQL-📚-트랜잭션Transaction-이란-💯-정리](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98Transaction-%EC%9D%B4%EB%9E%80-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC)

트랜잭션은 다음과 같은 과정을 가진다.

트랜잭션이 시작되면, 활동 상태가 되며, 트랜잭션이 정상적으로 실행되고 있음을 의미한다.

이 때, 작업이 성공하면, 활성 상태에서 Partially Commited 상태가 된다. 이 상태는 Commit 연산이 실행되기 직전으로, 작업이 성공했다고 무조건 반영하는 것이 아닌, 실제 DB에 작업 내용을 반영하지 않고 기다리는 상태를 말한다.

이후에 반영을 승인하는 상태를 Commit이라고 하며 이로써 트랜잭션이 성공적으로 종료된다.

작업 실패 과정은 다음과 같다. 하나는 트랜잭션 실행 자체에 오류가 발생해 중단된 상태로 Failed를 의미한다. 이후 트랜잭션이 비정상적인 종료를 해 Rollback 연산을 수행한 상태를 Aborted라고 한다. 이 Rollback을 수행하면 트랜잭션 내부의 작업을 수행 이전의 상태로 되돌린다.

---

DDL : Data Definition Language

- Create, Drop, Truncate, Alter

DML : Data Manipulation Language

- Select, Insert, Update, Delete

DCL : Data Control Language

- Grant, Revoke

DDL은 트랜잭션의 롤백 명령이 적용되지 않는다.

---

## 병행수행(Concurrency)

: DBMS는 여러 유저가 DB를 동시에 공유할 수 있도록 병행수행을 지원한다.

- 트랜잭션이 서로 다른 데이터를 사용할 때는 문제가 없으나, 동시에 같은 데이터에 접근해서 Read, Write를 한다면, 문제가 생길 수 있다.

### 갱신분실(Lost Update, Dirty Write)

<aside>
<img src="/icons/cherries_pink.svg" alt="/icons/cherries_pink.svg" width="40px" />

하나의 트랜잭션이 수행한 연산의 결과를 다른 트랜잭션이 덮으면서 변경된 연산이 무효화되는 것.

</aside>

### 모순성(Inconsistency, Non-repeatable Read)

### 연쇄 복귀(Cascading Rollback)
