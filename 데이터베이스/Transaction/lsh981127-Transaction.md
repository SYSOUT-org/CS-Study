# 🗄️트랜잭션이란?

- DB의 상태를 변경시키는 작업의 단위
- 한꺼번에 수행되어야 할 연산들을 모아둔 것
- 한번에 처리하거나, 한번에 원 상태로 복구하거나

# 🗃️ 트랜잭션의 특징(ACID)

### 1. Atomicity(원자성)

- 트랜잭션 내에서 진행되는 모든 연산들은 DB에 반영되거나, 전혀 반영되지 않아야한다.
- All or Nothing

### 2. Consistency(일관성)

- 트랜잭션의 작업 처리 결과는 항상 일관성 있는 DB 상태로 변환되야 한다.
- 트랜잭션 수행 전, 후의 시스템 상태가 같아야한다는 것

### 3. Isolation(독립성)

- 하나의 트랜잭션이 실행될 때, 다른 트랜잭션이 끼어들 수 없다.
- 수행 중인 트랜잭션이 완전히 완료될때까지, 해당 수행 결과를 참조할 수 없다.

### 4. Durability(영속성)

- 완료된 트랜잭션 결과는 시스템이 고장나도 영구적으로 반영되어야 한다.

# 🧮 트랜잭션 연산

### 1. Commit 연산

- 트랜잭션 성공적으로 수행됐다는 연산
- COMMIT 실행 ⇒ DB 반영

### 2. Rollback 연산

- 트랜잭션 수행이 실패 ⇒ 작업 취소하는 연산
- 일부 연산 처리 실패 ⇒ Rollback 연산 실행 ⇒ DB를 트랜잭션 수행 전으로 되돌리기

# ⛳ 트랜잭션의 상태

### 1. Active

- 활성 상태
- 트랜잭션 실행 중, 동작 중

### 2. Partially Committed

- COMMIT 연산 명령이 도착한 상태
- COMMIT 이전 SQL문 실행
    - 트랜잭션내의 연산들은 다 실행 완료된 상태
    - Commit 연산을 실행하기 직전의 상태

> **Partially Committed** 과 **Committed의 차리**
>
>
>
> - `COMMIT` 요청이 들어오면, **Partially Committed** 상태가 된다.
> - if (`COMMIT` 을 문제 없이 수행할 수 있으면) **Committed** 상태로 전이
> - else 오류가 발생한다면 **Failed** 상태가 된다.
>
> 즉, **Partially Committed** 는 `COMMIT` 요청이 들어왔을 때를 말하며,
>
> **Committed** 는 `COMMIT` 을 정상적으로 완료한 상태를 말한다.
>

### 3. Failed

- 트랜잭션 실패 상태

### 4. Committed

- 트랜잭션 완료 상태
- COMMIT 연산까지 실행된 상태

### 5. Aborted

- 트랜잭션 취소 상태
- 트랜잭션 취소 ⇒ 실행 이전 데이터로 돌아간 상태
    - ROLLBACK 연산을 실행한 상태

# 🖇️ 트랜잭션 격리 수준(Isolation Level)

- 여러 트랜잭션들이 처리될 때, 트랜잭션간 고립의 정도
    - 트랜잭션 특징(ACID) 중 Isolation(독립성, 고립성) 부분
- 트랜잭션이 다른 트랜잭션의 연산 결과를 얼마나 볼 수 있는지를 결정하는 것

## 격리 단계(밑으로 내려갈 수록 고립성 ⬆️)

### 1. READ UNCOMMITED(Level 0)

- 트랜잭션의 COMMIT, ROLLBACK과 상관없이 값을 읽을 수 있다.
- **정합성** 문제 야기 ⇒ **사용 X**
- ***DIRTY READ*** 현상이 발생할 수 있다.
<details>
<summary>DIRTY READ</summary>
<div markdown="1">

- 특정 트랜잭션 작업이 완료되지 않았지만 다른 트랜잭션에서 변경된 데이터를 읽는 상황

![dirtyread](https://velog.velcdn.com/images/shasha/post/27f8fb66-9f8c-4a16-a787-7715fb18ca38/image.png)
</div>
</details>
        


### 2. READ COMMITED(Level 1)

- Oracle의 Default
- 트랜잭션의 변경 내용이 **COMMIT**되어야 다른 트랜잭션에서 조회할 수 있는 단계
    - UNDO 세그먼트에 저장된 값들을 조회
        - UNDO 세그먼트
            - 트랜잭션이 발생했을 때, 변경하기 이전의 값을 저장하는 장소
- ***NON-REPEATABLE READ*** 문제 발생
<details>
<summary>NON-REPEATABLE READ</summary>
<div markdown="1">

- 트랜잭션에서 같은 쿼리 2번을 실행했으나 결과가 다른 현상
- 쿼리 호출 사이에 타 트랜잭션이 개입하여 ***일관성(ACID의 Consistency)*** 위반

![NonRepeatableRead](https://velog.velcdn.com/images/shasha/post/1efafadb-a1e1-421a-853b-dbb65e23349c/image.png)
</div>
</details>
        


### 3. REPEATABLE READ(Level 2)

- MySQL의 Default
- 트랜잭션이 시작하기 전, 커밋된 내용에 대해서만 READ 할 수 있는 단계
- Undo 세그먼트에
- ***PHANTOM READ*** 문제 발생
<details>
<summary>PHANTOM READ</summary>
<div markdown="1">

- 트랜잭션 시작할 때 읽었던 데이터에 존재하지 않다가 이후 다시 읽었을 때, 유령처럼 등장하는 현상
  - ***일관성(ACID의 Consistency)*** 위반
- 쿼리 호출 사이에 타 트랜잭션이 개입하여 ***일관성(ACID의 Consistency)*** 위반

![PhantomRead](https://velog.velcdn.com/images/shasha/post/68fecb14-c0e4-437a-8c45-9efc505aab00/image.png)

</div>
</details>
        


### 4. SERIALIZABLE(Level 3)

- 트랜잭션이 진행되는 동안, 다른 트랜잭션들은 ***READ***만 가능한 단계
- **모든 문제 방지 가능** BUT **동시성 성능 BAD**